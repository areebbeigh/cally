<template>
  <div id="app">
    <!-- {{quote}} -->
    <unsplash-photo :query="query" :quote="quote"></unsplash-photo>
    <div class="right-container">
      <div class="content">
        <span class="cal-wrapper">
          <calendar-widget></calendar-widget>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
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

  data: function() {
    return {
      quote: JSON.stringify({
        quote: "With great power comes great responsibility.",
        by: "Dying Old Man"
      }),
      query: "January"
    };
  }
};
</script>

<style>
#app {
  display: grid;
  grid-template-columns: 4fr 6fr;
  height: 100vh;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.right-container .content {
  display: grid;
  grid-template-rows: 6fr 4fr;
  padding: 5%;
}

@media screen and (max-width: 1220px) {
  #app {
    grid-template-columns: 1fr;
  }

  body {
    overflow-y: visible;
  }
}
</style>
