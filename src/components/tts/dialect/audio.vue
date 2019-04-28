<template>
  <div>
    <audio ref="audioDom" :src="songUrl" @canplay="ready" @error="error" @ended="end"></audio>
  </div>
</template>

<script>
export default {
  props: {
    playing: {
      type: Boolean,
      default: false
    },
    songUrl: {
      type: String,
      default: "/static/szj.wav"
    },
    songVolume: {
      type: Number,
      default: 0.5
    },
    songRate: {
      type: Number,
      default: 1
    }
  },
  data() {
    return {
      songReady: true
    };
  },
  methods: {
    playSong() {
      if (!this.songReady) return;
      this.$nextTick(() => {
        this.$refs.audioDom.playbackRate = this.songRate;
        this.$refs.audioDom.play();
      });
      this.songReady = false;
    },
    ready() {
      this.songReady = true;
    },
    error() {
      this.songReady = true;
    },
    end() {
      this.$emit('playend',false)
    }
  },
  watch: {
    songVolume(newVal) {
      this.$refs.audioDom.volume = newVal;
    },
    songRate(newVal) {
      this.$refs.audioDom.playbackRate = newVal;
    },
    playing(newPlaying) {
      if (!this.songReady) return;
      const audio = this.$refs.audioDom;
      this.$nextTick(() => {
        newPlaying ? audio.play() : audio.pause();
      });
    }
  }
};
</script>

