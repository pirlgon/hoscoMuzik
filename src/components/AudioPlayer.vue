<template>
  <div id="audio-template">
    <div id="artwork">
      <img :src="currentSong.artworkUrl100" v-show="currentSong.artworkUrl100!=''">
    </div>
    <div id="songinfo">
      <p>
        <label>Artist :</label>
        {{ currentSong.artistName }}
      </p>
      <p>
        <label>Song :</label>
        {{ currentSong.trackName }}
      </p>
      <p>
        <label>Collection :</label>
        {{ currentSong.collectionName }}
      </p>
      <p>
        <label>Duration :</label>
        {{ currentSong.trackDuration }}
      </p>
    </div>
    <audio controls preload="none" style="width:480px;" id="zikListener" ref="zikListener">
      <source :src="currentSong.previewUrl" type="audio/mp4">
      <p>Your browser does not support HTML5 audio.</p>
    </audio>
    <div id="audionav">
      <button v-on:click="previousSong" id="previousSong" title="Previous Song"></button>
      <button v-on:click="nextSong" id="nextSong" title="Next Song"></button>
      <button v-on:click="backToResult" id="backToResult" title="Back to list"></button>
    </div>
    <share
      :currentSong="currentSong"
      id="share"
    ></share>
  </div>
</template>

<script>
import Vue from "vue";
import Share from "./Share";

export default {
  name: "audioplayer",
  template: "#audio-template",
  components: {
    Share
  },
  props: {
      currentSong: Object
  },
  methods: {
    initSong() {
      Vue.nextTick(() => {
        this.$refs.zikListener.load();
        this.$refs.zikListener.play();

        document.title = this.currentSong.trackName + " | " + this.currentSong.artistName;

        document.description =
          "i'm listening on " + this.currentSong.trackName + " by " + this.currentSong.artistName;
        document
          .querySelector('meta[property="og:title"]')
          .setAttribute(
            "content",
            "i'm listening on " + this.currentSong.trackName + " by " + this.currentSong.artistName
          );
        document
          .querySelector('meta[property="og:description"]')
          .setAttribute(
            "content",
            "i'm listening on " + this.currentSong.trackName + " by " + this.currentSong.artistName
          );
        document
          .querySelector('meta[property="og:image"]')
          .setAttribute("content", this.currentSong.artworkUrl100);
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
  padding: 10px;
  background-color: #42b983;
  position: relative;
}
audio {
  margin-bottom: 10px;
}
#audio-template button {
  width: 64px;
  height: 64px;
  border: 0;
  cursor: pointer;
  background-color: #42b983;
  border: 1px solid white;
  border-radius: 5px;
}
#audio-template p{
    margin-top: 8px;
    margin-bottom: 8px;
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
#audionav {
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
#share {
  top: 5px;
  position: absolute;
  right: 5px;
}
</style>
