<template>
  <div id="app">
    <h1>Hosco Muzik</h1>
    <div v-show="!player">
      <p>{{ message }}</p>
      <input v-model="searched">
      <button v-on:click="searchOnItunes" v-on:keyup.enter="searchOnItunes">OK</button>
      <p>{{ resultMessage }}</p>
    </div>
    <grid
      :data="result"
      :columns="gridColumns"
      v-on:listen="changeSong($event)"
      v-on:sort="changeOrder($event)"
      v-show="!player && result.length > 0"
    ></grid>
    <audioplayer
      :songArtwork="songArtwork"
      :songUrl="songUrl"
      :songName="songName"
      :songArtist="songArtist"
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
      gridColumns: ["artistName", "trackName", "collectionName"],
      songUrl: "",
      songArtwork: "",
      songRang: 0,
      songName: "",
      songArtist: "",
      audio: "",
      player: false
    };
  },
  methods: {
    searchOnItunes() {
      axios
        .get("https://itunes.apple.com/search?term=" + this.searchedTerms)
        .then(response => {
          this.result = [];
          response.data.results.forEach(song => {
            this.result.push(song);
          });
          this.resultCount = response.data.resultCount;
        })
        .catch(function(error) {
          this.result = error;
        });
    },
    changeSong(rang) {
      this.songUrl = this.result[rang].previewUrl;
      this.songArtwork = this.result[rang].artworkUrl100;
      this.songRang = rang;
      this.songName = this.result[rang].trackName;
      this.songArtist = this.result[rang].artistName;

      console.log(this.result[rang].trackName);

      this.player = true;
      this.$refs["audioplayer"].initSong();
    },
    changeOrder(filteredData) {
      this.result = filteredData;
      console.log(filteredData);
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
</script>

<style>
body{
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
