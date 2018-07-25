<template>
  <div>
    <h1>Did you know? <hr></h1>
    <transition name="slide-fade">
      <ul v-if="factToday && factRandYr">
        <li>{{factToday}}</li>
        <li>{{factRandYr}}</li>
      </ul>
    </transition>
  </div>
</template>

<script>
import axios from "axios";

import eventBus from "../main";

export default {
  name: "OnThisDayWidget",

  data: function() {
    return {
      factToday: null,
      factRandYr: null
    };
  },

  // asyncComputed: {
  //   onThisDay: async function() {
  //     const BASE_URL = "http://history.muffinlabs.com/date/";
  //     let reqUrl = BASE_URL + (this.selected.month + 1) + "/" + this.selected.date;

  //     return await axios.get(reqUrl);
  //   }
  // },

  created: function() {
    eventBus.$on("dateUpdated", selected => {
      const BASE_URL = "https://numbersapi.com";
      const RANDOM_YEAR = BASE_URL + "/random/year";
      let dateUrl = BASE_URL + "/" + (selected.month + 1) + "/" + selected.date + "/date";

      this.factToday = null;
      this.factRandYr = null;

      axios.get(dateUrl).then(x => (this.factToday = x.data));
      axios.get(RANDOM_YEAR).then(x => (this.factRandYr = x.data));
    });
  },

  mounted: function() {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div {
  color: #7d7c7d;
}

ul li {
  margin-top: 5%;
}

hr {
  width: 40%;
  display: none;
  margin-left: 0;
}

.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
</style>
 