<style lang="scss" scoped>
.dialect {
  ul {
    display: flex;
    flex-wrap: wrap;
    li {
      width: 140px;
      height: 32px;
      border: 1px solid #ccc;
      margin: 10px;
      padding: 5px 10px;
    }
  }
  .listContent {
    margin-top: 10px;
    .left {
      float: left;
    }
    .content {
      width: 25%;
      padding-bottom: 10px;
      padding-right: 10px;
      cursor: pointer;
      .contentBox {
        height: 32px;
        border: 1px solid #dcdfe6;
        font-size: 12px;
        border-radius: 4px;
        padding: 8px 15px 10px;
        transition: all 0.3s;
        position: relative;
        img {
          position: absolute;
          right: 10px;
          top: 6px;
          width: 20px;
          height: 20px;
        }
        .mdplay {
          position: absolute;
          right: 10px;
          top: 9px;
          font-size: 14px;
        }
      }
    }
  }
}
</style>

<template>
  <div class="dialect">
    <player
      ref="player"
      :songUrl="songUrl"
      :songVolume="songVolume"
      :songRate="songRate"
      @playend="playend"
    ></player>
    <div class="listContent clear">
      <div class="content left" v-for="(item, index) in soundData" :key="item.speakId">
        <div
          class="contentBox"
          :style="{ borderColor: radio == item.speakId? '#4DA1FF': '#DCDFE6' }"
          @click="checkBoxPick(index)"
        >
          <el-radio v-model="radio" :label="item.speakId">{{item.label}}</el-radio>
          <img src="/static/runningVoice.gif" v-show="imgPk == item.speakId">
          <i
            class="el-icon-caret-right mdplay"
            v-show="imgPk !== item.speakId"
            @click="playSong(index)"
          ></i>
          <!-- <Icon
            class="mdplay"
            type="md-play"
            v-show="imgPk !== item.speakId"
            @click="playSong(index)"
          />-->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import player from "@/components/tts/dialect/audio";
export default {
  props: {
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
      songUrl: null,
      radio: null,
      imgPk: "",
      soundData: [
        { label: "女声1", speakId: "szj", url: "/static/szj.wav" },
        { label: "女声2", speakId: "2", url: "/static/mh.wav" },
        { label: "女声3", speakId: "3", url: "/static/yq.wav" },
        { label: "女声4", speakId: "4", url: "/static/tz.wav" },
        { label: "女声5", speakId: "5", url: "/static/lcl.wav" },
        { label: "女声6", speakId: "6", url: "/static/chm.wav" },
        { label: "女声7", speakId: "7", url: "/static/sy.wav" }
      ]
    };
  },
  mounted() {},
  methods: {
    checkBoxPick(i) {
      this.radio = this.soundData[i].speakId;
    },
    playSong(i) {
      this.radio = this.soundData[i].speakId;
      this.imgPk = this.soundData[i].speakId;
      this.$nextTick(() => {
        this.songUrl = this.soundData[i].url;
        this.$refs.player.playSong();
      });
    },
    playend() {
      this.imgPk = "";
    }
  },
  components: {
    player
  }
};
</script>

