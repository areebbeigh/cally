<template>
  <div v-bind:class="{'wide-screen': wideScreen}" class="left-container" :style="bgImgStyle">
    <!-- {{wideScreen}} -->
    <div v-on:click="eventBus.$emit('wideScreenOn')" class="hide-button">&lt;</div>
    <div class="content">
      <div>
        <blockquote>
          <h1>{{quoteJson.body}}</h1>
          <p>
            <strong>- {{quoteJson.author}} -</strong>
          </p>
        </blockquote>
      </div>

    </div>
  </div>
</template>

<script>
import eventBus from "../main";

export default {
  name: "UnsplashPhoto",

  props: {
    query: String,
    quote: String
  },

  created: function() {
    eventBus.$on("wideScreenOn", () => {
      this.wideScreen = true;
    });

    eventBus.$on("wideScreenOff", () => {
      this.wideScreen = false;
    });
  },

  data: function() {
    return {
      eventBus,
      wideScreen: false
    };
  },

  computed: {
    backgroundImg: function() {
      let baseUrl = "https://source.unsplash.com/random";
      return baseUrl + "?" + this.query;
    },

    bgImgStyle: function() {
      return (
        "background: linear-gradient( rgba(0, 0, 0, 0.5), \
        rgba(0, 0, 0, 0.5) ), url('" +
        this.backgroundImg +
        "');"
      );
    },

    quoteJson: function() {
      return JSON.parse(this.quote);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.left-container {
  height: 100%;
  background-color: #222;
  background-size: cover !important;
  background-position: center center;
  background-repeat: no-repeat;
}

.left-container .content {
  display: grid;
  grid-template-rows: repeat(1fr, 2);
  /* padding: 20px; */
  height: 100%;
  color: white;
}

.left-container blockquote {
  margin: 0;
  position: relative;
  top: 40%;
  margin: 0 10%;
  /* padding: 0; */
  /* font-size: 1.4em; */
  text-align: center;
  border: none;
}

.left-container blockquote p {
  /* text-align: right; */
  font-style: italic;
}

.hide-button {
  /* display: none; */
  color: #222;
  font-weight: bold;
  font-size: 1.3em;
  width: min-content;
  background: #fff;
  padding: 1%;
  /* border: 1px solid #563a3a; */
  border-radius: 2em 0 0 2em;
  cursor: pointer;
  position: absolute;
  top: 1%;
  left: 37.7%;
  /* bottom: 1%; */
  /* z-index: -1; */
}

.wide-screen {
  display: none;
}

@media screen and (max-width: 1220px) {
  .hide-button {
    display: none;
  }

  .left-container {
    height: 50vh;
  }

  .left-container blockquote {
    top: 10%;
  }
}
</style>
 