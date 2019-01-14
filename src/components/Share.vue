<template>
  <div>
    <a
    
      :href="'https://www.facebook.com/sharer/sharer.php?u='+currentSong.trackViewUrl"
      target="_blank"
      id="facebook"
    >
      <img src="../assets/facebook.png">
    </a>
    <a
      class="twitter-share-button"
      :href="'https://twitter.com/intent/tweet?text=Just%20listening%20to%20'+currentSong.trackName+'%20by%20'+currentSong.artistName"
      target="_blank"
      id="twitter"
    >
      <img src="../assets/twitter.png"/>
    </a>
  </div>
</template>

<script>
export default {
  name: "audioplayer",
  template: "#audio-template",
  props: {
    currentSong: Object
  }
};
</script>
