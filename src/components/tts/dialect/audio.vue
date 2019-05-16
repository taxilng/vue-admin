<template>
  <div>
    <audio
      ref="audioDom"
      :src="songUrl"
      @canplay="ready"
      @error="error"
      @ended="end"
      @timeupdate="updateTime"
      :controls="showControls"
    ></audio>
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
    },
    showControls: {
      type: Boolean,
      default: false
    },
  },
  data () {
    return {
      songReady: true,
      currentTime: 0,
    };
  },
  methods: {
    playSong () {
      if (!this.songReady) return;
      this.$nextTick(() => {
        this.$refs.audioDom.play();
        this.$refs.audioDom.playbackRate = this.songRate;
      });
      this.songReady = false;
    },
    ready () {
      this.songReady = true;
    },
    error () {
      this.songReady = true;
      this.$emit('playStatus', 'end')
    },
    end () {
      this.$emit('playStatus', 'end')
    },
    updateTime (e) {
      if (this.$refs.audioDom.paused) {
        this.$emit('playStatus', 'pause')
      } else {
        this.$emit('playStatus', 'play')
      }
      const muted = this.$refs.audioDom.muted
      // console.log(muted);
      if(muted){
        this.$emit('changeVolume', 0)
      }else{
        this.$emit('changeVolume', this.$refs.audioDom.volume)
      }
      
    },
  },
  watch: {
    songVolume (newVal) {
      this.$refs.audioDom.volume = newVal;
    },
    songRate (newVal) {
      this.$refs.audioDom.playbackRate = newVal;
    },
    playing (newPlaying) {
      if (!this.songReady) return;
      const audio = this.$refs.audioDom;
      this.$nextTick(() => {
        newPlaying ? audio.play() : audio.pause();
      });
    },
    songUrl (newVal) {
      if (!this.songReady) return;
      const audio = this.$refs.audioDom;
      this.$nextTick(() => {
        newVal ? audio.play() : '';
      });
    }
  }
};
</script>

