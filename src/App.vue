<template>
  <div id="app" v-bind:class="{'wide-screen': wideScreen}">
    <!-- {{quote}} -->
    <div v-on:click="eventBus.$emit('wideScreenOff')" class="hide-button-left">&gt;</div>
    <div class="container">
      <unsplash-photo :query="query" :quote="quote"></unsplash-photo>
      <div class="right-container">
        <div class="content">
          <div class="cal-wrapper">
            <calendar-widget></calendar-widget>
          </div>
          <div class="facts-wrapper">
            <on-this-day-widget></on-this-day-widget>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

import eventBus from "./main";

import UnsplashPhoto from "./components/UnsplashPhoto.vue";
import CalendarWidget from "./components/CalendarWidget.vue";
import OnThisDayWidget from "./components/OnThisDayWidget.vue";

export default {
  name: "app",
  
  components: {
    UnsplashPhoto,
    CalendarWidget,
    OnThisDayWidget
  },

  mounted: function() {
    eventBus.$on("wideScreenOn", () => {
      this.wideScreen = true;
    });

    eventBus.$on("wideScreenOff", () => {
      this.wideScreen = false;
    });

    const reqUrl = "https://favqs.com/api/qotd";

    axios.get(reqUrl).then(x => {
      if (x.data.quote.body.length < 180)
        this.quote = JSON.stringify(x.data.quote)
    })
  },

  data: function() {
    return {
      eventBus,
      wideScreen: false,
      // Default quote
      quote: JSON.stringify({
        body: "It is the mark of an educated mind to be able to entertain a thought without accepting it.",
        author: "Aristotle"
      }),
      query: "Nature"
    };
  }
};
</script>

<style>
.container {
  display: grid;
  grid-template-columns: 4fr 6fr;
  height: 100vh;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.right-container .content {
  /* display: grid; */
  /* grid-template-rows: 6fr 4fr; */
  margin: 0 auto;
  padding: 5%;
}

.cal-wrapper {
  width: 60%;
  margin: 0 auto;
}

.facts-wrapper {
  width: 60%;
  margin: 0 auto;
  margin-top: 80px;
}

.hide-button-left {
  /* display: none; */
  color: #fff;
  display: none;
  font-weight: bold;
  font-size: 1.3em;
  width: min-content;
  background: #009688;
  padding: 1%;
  /* border: 1px solid #563a3a; */
  border-radius: 0 2em 2em 0;
  cursor: pointer;
  position: absolute;
  top: 1%;
  left: -0.5%;
  /* bottom: 1%; */
  /* z-index: -1; */
}

.wide-screen .hide-button-left {
  display: block;
}

.wide-screen .container {
  grid-template-columns: 1fr;
}

.wide-screen .content {
  display: grid;
  grid-template-columns: 1fr 1fr;
}

.wide-screen .cal-wrapper {
  margin: 0;
  margin-left: 30%;
  width: 70%;
}

.wide-screen .facts-wrapper {
  margin: 0 0 0 0;
  padding-left: 5%;
  padding-top: 10%;
  margin-left: 5%;
  border-left: 1px solid #7d7c7d;
}

@media screen and (max-width: 1220px) {
  .container {
    grid-template-columns: 1fr;
  }

  .cal-wrapper {
    width: 100%;
  }

  .facts-wrapper {
    width: 100%;
  }
}
</style>
