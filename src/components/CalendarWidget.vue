<template>
  <div class="calendar">
    <div class="grid-7">
      <div v-on:click="goToToday" class="date-today">
        <div style="text-align: center">
          <h2>{{days[date.getDay()]}}</h2>
          <p>
            {{date.getDate()}}
            {{months[date.getMonth()]}}
            {{date.getFullYear()}}
          </p>
        </div>
      </div>

      <el-select 
        class="cal-selector select-month" 
        v-model="selected.month" 
        v-on:change="emitDateUpdate()"
        placeholder="Select">
        <el-option
          v-for="month in months"
          :key="month"
          :value="months.indexOf(month)"
          :label="month">
          <span style="float: left">{{ month }}</span>
          <span style="float: right; color: #8492a6; font-size: 13px">{{ months.indexOf(month) + 1 }}</span>
        </el-option>
      </el-select>

      <el-select 
        class="cal-selector select-year"
        v-model="selected.year" 
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

      <div class="col week-days-row date-line"
        v-for="day in days"
        v-bind:key="day">
        <span class="week-day">{{day.slice(0,1)}}</span>
      </div>

      <div class="col"
        v-for="prevDate in calendar.previousDates"
        v-bind:key="'prevDate_'+prevDate">
        <el-button 
          disabled
          class="date-button-previous date-button">
          {{prevDate}}
        </el-button>
      </div>

      <div class="col"
        v-for="calDate in calendar.dates"
        v-bind:key="'date_'+calDate">
        <el-button  
          class="date-button"
          v-on:click="changeSelectedDate(calDate)"
          v-bind:class="{ 
            'date-button-active': isCurrentMonthAndYear && calDate == today.getDate(), 
            'date-button-false': !isCurrentMonthAndYear && calDate == today.getDate(),
            'date-button-selected': selected.date == calDate 
            }">
          {{calDate}}
        </el-button>
      </div>
    </div>
  </div>
</template>

<script>
import eventBus from "../main";

export default {
  name: "CalendarWidget",

  mounted: function() {
    this.emitDateUpdate();
  },

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

      selected: {
        date: new Date().getDate(),
        month: new Date().getMonth(),
        year: new Date().getFullYear()
      }
    };
  },

  computed: {
    today: function() {
      return new Date();
    },

    isCurrentMonthAndYear: function() {
      return (
        this.selected.year == this.today.getFullYear() &&
        this.selected.month == this.today.getMonth()
      );
    },

    selectableYears: function() {
      const lowerLimit = 1200;
      let possibleStart = this.selected.year - 50;
      let possibleEnd = this.today.getFullYear() + 50;

      let start = possibleStart > lowerLimit ? possibleStart : lowerLimit;
      let end =
        possibleEnd > this.selected.year ? possibleEnd : this.selected.year + 50;

      return new Array(end - start + 1)
        .fill()
        .map((dummy, index) => start + index);
    },

    calendar: function() {
      let year = this.selected.year;
      let month = this.selected.month;

      let cal = {
        year,
        month,
        monthName: this.months[month],
        startDay: new Date(year, month, 1).getDay(),
        numberOfDays: this.getDaysInMonth(year, month),
        previousDates: [],
        dates: []
      };

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

        cal.dates.push(date);
      }

      return cal;
    }
  },

  methods: {
    emitDateUpdate: function() {
      eventBus.$emit("dateUpdated", Object.assign({}, this.selected));
    },

    getDaysInMonth: function(year, month) {
      // Month is 0 based. Third parameter is date (1 based).
      // Inputting 0 as date gives the last date of the previous month.
      return new Date(year, month + 1, 0).getDate();
    },

    changeSelectedDate: function(date) {
      this.selected.date = date;
      this.emitDateUpdate();
    },

    goToToday: function() {
      this.selected.month = this.date.getMonth();
      this.selected.year = this.date.getFullYear();
      this.selected.date = this.date.getDate();
      this.emitDateUpdate();
    }
  },

  // watch: {
  //   selected: function(val) {
  //     this.emitDateUpdate();
  //   }
  // }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calendar {
  width: 100%;
  /* border: 1px solid #000; */
}

.grid-7 {
  display: grid;
  grid-column-gap: 20px;
  grid-row-gap: 15px;
  grid-template-columns: repeat(7, 1fr);
  grid-auto-rows: repeat(100px, auto);
}

.date-today {
  grid-row: 1/3;
  grid-column: 1/4;
  clip-path: polygon(0 0, 80% 0, 100% 50%, 80% 100%, 0 100%);
  background: #009688;
  color: white;
  cursor: pointer;
}

.date-today div {
  margin-right: 20%;
  font-size: 1.2em;
}

.date-today div p {
  margin-top: 0;
}

.date-today div h2 {
  margin-bottom: 2%;
}

.select-month {
  grid-column: 5/8;
}

.select-year {
  grid-column: 5/8;
}

.cal-selector {
  /* padding: 20px; */
}

/* .el-select input[type="text"] {
  border: none !important;
  font-size: 4.5em !important;
  height: auto;
} */

.week-days-row {
  margin-bottom: 20px;
  margin-top: 10px;
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

.date-button:focus {
  background: #4db6ac;
  color: #fff;
}

.date-button-selected {
  background: #4db6ac;
  color: #fff;
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
 