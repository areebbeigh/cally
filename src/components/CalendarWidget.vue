<template>
  <div class="calendar">
    <!-- {{year}}
    <button v-on:click="yearUp">Year up</button>
    <button>Month up</button> -->
    <!-- {{calendar}}<br><br> -->
    <div class="row cols-2">
      <div class="col">
        <el-select class="select-month" v-model="selectedMonth" placeholder="Select">
          <el-option
            v-for="month in months"
            :key="month"
            :value="months.indexOf(month)"
            :label="month">
            <span style="float: left">{{ month }}</span>
            <span style="float: right; color: #8492a6; font-size: 13px">{{ months.indexOf(month) + 1 }}</span>
          </el-option>
        </el-select>
      </div>
      
      
      <div class="col">
        <el-select 
          v-model="selectedYear" 
          filterable 
          allow-create 
          default-first-option
          placeholder="Select">
          <el-option
            v-for="year in selectableYears"
            :key="'yr_'+year"
            :label="year.toString()" 
            :value="year">
              {{year}}
          </el-option>
        </el-select>
      </div>
    </div>

    <div class ="row cols-7 week-days-row date-line">
      <div class="col"
        v-for="day in days"
        v-bind:key="day">
        <span class="week-day">{{day.slice(0,1)}}</span>
      </div>
      
    </div>

    <div class="row cols-7 date-line"
      v-for="calDates in calendar.dates" 
      v-bind:key="'dates_'+calDates.join('')">
      
      <div class="col"
        v-if="calendar.dates.indexOf(calDates) == 0 && calendar.previousDates.length"
        v-for="prevDate in calendar.previousDates"
        v-bind:key="'prevDate_'+prevDate">
        <el-button 
          disabled
          class="date-button-previous date-button">
          {{prevDate}}
        </el-button>
      </div>

      <div class="col"
        v-for="calDate in calDates"
        v-bind:key="'date_'+calDate">
        <el-button  
          class="date-button"
          v-bind:class="{ 'date-button-active': isCurrentDate && calDate == today.getDate(), 
          'date-button-false': !isCurrentDate && calDate == today.getDate() }">
          {{calDate}}
        </el-button>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: "CalendarWidget",

  data: function() {
    return {
      date: new Date(),
      currentDate: new Date().getDate(),

      months: [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ],

      days: [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday"
      ],

      selectedMonth: new Date().getMonth(),
      selectedYear: new Date().getFullYear()
    };
  },

  computed: {
    today: function() {
      return new Date();
    },

    isCurrentDate: function() {
      return (
        this.selectedYear == this.today.getFullYear() &&
        this.selectedMonth == this.today.getMonth()
      );
    },

    selectableYears: function() {
      const lowerLimit = 1200;
      let possibleStart = this.selectedYear - 50;
      let possibleEnd = this.today.getFullYear() + 50;

      let start = possibleStart > lowerLimit ? possibleStart : lowerLimit;
      let end =
        possibleEnd > this.selectedYear ? possibleEnd : this.selectedYear + 50;

      return new Array(end - start + 1)
        .fill()
        .map((dummy, index) => start + index);
    },

    calendar: function() {
      let year = this.selectedYear;
      let month = this.selectedMonth;

      let cal = {
        year,
        month,
        monthName: this.months[month],
        startDay: new Date(year, month, 1).getDay(),
        numberOfDays: this.getDaysInMonth(year, month),
        previousDates: [],
        dates: []
      };

      let dateLine = [];
      for (let date = 1; date <= cal.numberOfDays; date++) {
        if (date == 1) {
          if (cal.startDay) {
            // Fill first dateline with previous month dates if needed
            let daysInPrevMonth = this.getDaysInMonth(cal.year, cal.month - 1);
            let start = daysInPrevMonth - cal.startDay + 1;

            for (
              let prevDate = start;
              prevDate <= daysInPrevMonth;
              prevDate++
            ) {
              cal.previousDates.push(prevDate);
            }
          }
        }

        dateLine.push(date);

        if (
          dateLine.length == 7 ||
          date == cal.numberOfDays ||
          (!cal.dates.length && dateLine.length == 7 - cal.startDay)
        ) {
          cal.dates.push(Object.assign([], dateLine));
          dateLine = [];
        }

        // console.log(cal.dates, dateLine)
      }

      return cal;
    }
  },

  methods: {
    // yearUp: function() {
    //   let date = new Date();
    //   date.setFullYear(this.date.getFullYear() + 1);
    //   this.date = date;
    // },

    // getDay: function() {
    //   return this.date.getDay();
    // },

    getDaysInMonth: function(year, month) {
      // Month is 0 based. Third parameter is date (1 based).
      // Inputting 0 as date gives the last date of the previous month.
      return new Date(year, month + 1, 0).getDate();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calendar {
  width: 60%;
}

.row {
  margin-bottom: 10px;
}

.cols-2 {
  display: grid;
  grid-template-columns: 1fr 1fr;
}

.cols-7 {
  display: grid;
  grid-column-gap: 20px;
  grid-template-columns: repeat(7, 1fr);
}

/* .el-select input[type="text"] {
  border: none !important;
  font-size: 4.5em !important;
  height: auto;
} */

.week-days-row {
  margin-bottom: 30px;
  margin-top: 30px;
}

.week-day {
  min-width: 60px;
  width: 100%;
  /* width: 60px; */
  /* text-align: center; */
}

.date-line {
  text-align: center;
}

.date-button-active {
  background: #009688;
  color: white;
}

.date-button-false {
  border: 2px #009688 dotted;
}

.date-button {
  /* min-width: 60px; */
  width: 100%;
}

.date-button-active {
  background: #009688;
  color: white;
}

.date-button-active:hover {
  background: #009688;
  color: white;
}

.date-button-previous {
  cursor: pointer !important;
}

@media screen and (max-width: 1220px) {
  .calendar {
    width: 100%;
  }

  .date-button {
    min-width: 5px;
  }
}
</style>
 