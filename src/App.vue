<template>
  <div id="app">
    <h1>Hosco Muzik</h1>
    <div v-show="!player">
      <p>{{ message }}</p>
      <input v-model="searched" v-on:keyup.enter="searchOnItunes">
      <button v-on:click="searchOnItunes" >OK</button>
      <p v-show="!loading">{{ resultMessage }}</p>
    </div>
    <img src="./assets/wait.gif" v-show="loading">
    <grid
      :data="result"
      :columns="gridColumns"
      v-on:listen="changeSong($event)"
      v-on:sort="changeOrder($event)"
      v-show="!player && result.length > 0 && !loading"
    ></grid>
    <audioplayer
      :currentSong="currentSong"
      v-show="player"
      v-on:nextSong="nextSong"
      v-on:prevSong="previousSong"
      v-on:back="backToResult"
      ref="audioplayer"
    ></audioplayer>
  </div>
</template>

<script>
import Vue from "vue";
import axios from "axios";
import Grid from "./components/Table";
import Audioplayer from "./components/AudioPlayer";

export default {
  name: "app",
  components: {
    Grid,
    Audioplayer
  },
  data() {
    return {
      message: "Enter your search !",
      searched: "",
      result: [],
      resultCount: 0,
      gridColumns: [
        "artistName",
        "trackName",
        "trackPrice",
        "collectionName",
        "primaryGenreName",
        "trackDuration"
      ],
      currentSong: { artworkUrl100: "", artistName: "", trackName: "" },
      songRang: 0,
      audio: "",
      player: false,
      loading: false
    };
  },
  methods: {
    searchOnItunes() {
      this.loading = true;
      axios
        .get("https://itunes.apple.com/search?term=" + this.searchedTerms)
        .then(response => {
          this.result = [];
          response.data.results.forEach(song => {
            song.trackDuration = parseMilliseconds(song.trackTimeMillis);
            this.result.push(song);
          });
          this.resultCount = response.data.resultCount;
          this.loading = false;
        })
        .catch(function(error) {
          this.result = error;
          this.loading = false;
        });
    },
    changeSong(rang) {
      this.currentSong = this.result[rang];
      this.songRang = rang;

      this.player = true;
      this.$refs["audioplayer"].initSong();
    },
    changeOrder(fiteredData) {
      this.result = fiteredData;
      console.log(fiteredData);
    },
    nextSong() {
      if (this.songRang < this.result.length - 1) {
        this.songRang += 1;
      } else {
        this.songRang = 0;
      }
      this.changeSong(this.songRang);
    },
    previousSong() {
      if (this.songRang > 0) {
        this.songRang -= 1;
      } else {
        this.songRang = this.result.length - 1;
      }
      this.changeSong(this.songRang);
    },
    backToResult() {
      document.title = "Hosco Muzik";
      this.player = false;
    }
  },
  computed: {
    searchedTerms() {
      return this.searched.replace(" ", "+");
    },
    resultMessage() {
      let message = "";
      if (this.resultCount == 0) {
        message = "There is no result.";
      } else {
        let plural = "";
        if (this.resultCount > 1) {
          plural = "s";
        }
        message = "There is " + this.resultCount + " result" + plural;
      }
      return message;
    }
  }
};

function parseMilliseconds(milliseconds) {
  var hours = milliseconds / (1000 * 60 * 60);
  var absoluteHours = Math.floor(hours);
  var h = absoluteHours > 9 ? absoluteHours : "0" + absoluteHours;

  var minutes = (hours - absoluteHours) * 60;
  var absoluteMinutes = Math.floor(minutes);
  var m = absoluteMinutes > 9 ? absoluteMinutes : "0" + absoluteMinutes;

  var seconds = (minutes - absoluteMinutes) * 60;
  var absoluteSeconds = Math.floor(seconds);
  var s = absoluteSeconds > 9 ? absoluteSeconds : "0" + absoluteSeconds;

  return h + ":" + m + ":" + s;
}
</script>

<style>
body {
  background-color: black;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  margin-top: 60px;
}
</style>
