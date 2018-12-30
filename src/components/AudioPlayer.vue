<template>
  <div id="audio-template">
    <div id="artwork">
      <img :src="songArtwork" v-show="songArtwork!=''">
    </div>
    <div id="songinfo">
      <p>
        <label>Artist :</label>
        {{ songArtist }}
      </p>
      <p>
        <label>Song :</label>
        {{ songName }}
      </p>
      <p>
        <label>Collection :</label>
      </p>
    </div>
    <audio controls preload="none" style="width:480px;" id="zikListener" ref="zikListener">
      <source :src="songUrl" type="audio/mp4">
      <p>Your browser does not support HTML5 audio.</p>
    </audio>
    <div id="audionav">
      <button v-on:click="previousSong" id="previousSong" title></button>
      <button v-on:click="nextSong" id="nextSong" title="Next Song"></button>
      <button v-on:click="backToResult" id="backToResult" title="Back to list"></button>
    </div>
  </div>
</template>

<script>
import Vue from "vue";

export default {
  name: "audioplayer",
  template: "#audio-template",
  props: {
    songArtwork: String,
    songUrl: String,
    songName: String,
    songArtist: String
  },
  methods: {
    initSong() {
      Vue.nextTick(() => {
        this.$refs.zikListener.load();
        this.$refs.zikListener.play();
      });
    },
    previousSong() {
      this.$emit("prevSong");
      this.initSong();
    },
    nextSong() {
      this.$emit("nextSong");
      this.initSong();
    },
    backToResult() {
      this.$emit("back");
      this.$refs["zikListener"].pause();
    }
  }
};
</script>
<style>
#audio-template {
  width: 480px;
  margin: auto;
  text-align: left;
  border-radius: 10px;
  border: 2px solid black;
  padding:10px;
  background-color: #42b983
}
audio{
  margin-bottom:10px;
}
#audio-template button {
  width: 64px;
  height: 64px;
  border: 0;
  cursor: pointer;
  background-color: #42b983;
  border: 1px solid white;
  border-radius:5px;
}
#artwork {
  float: left;
  width: 100px;
  height: 100px;
  margin-bottom: 10px;
}
#songinfo label {
  font-weight: bold;
  font-style: italic;
  width: 80px;
  display: inline-block;
  padding-left: 5px;
}
#audionav{
  text-align: center;
}
#previousSong {
  background-image: url("../assets/prevsong.png");
}
#nextSong {
  background-image: url("../assets/nextsong.png");
  margin-right: 10px;
  margin-left: 10px;
}
#backToResult {
  background-image: url("../assets/back.png");
}
</style>
