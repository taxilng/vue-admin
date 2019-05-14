
<template>
  <div class="dialect">
    <player
      ref="player"
      :songUrl="songUrl"
      :songVolume="songVolume"
      :songRate="songRate"
      @playStatus="playStatus"
    ></player>
    <div class="listContent clear">
      <el-row :gutter="10">
        <el-col
          :xs="12"
          :sm="6"
          lass="content"
          v-for="(item, index) in typesSound"
          :key="index"
        >
          <div class="content">
            <div
              class="contentBox"
              :style="{
                borderColor: radio == item.speakId ? '#4DA1FF' : '#DCDFE6'
              }"
              @click="checkBoxPick(item.speakId)"
            >
              <el-radio v-model="radio" :label="item.speakId">{{
                item.label
              }}</el-radio>
              <img
                src="/static/runningVoice.gif"
                v-show="imgPk == item.speakId"
              />
              <i
                class="el-icon-caret-right mdplay"
                v-show="imgPk !== item.speakId"
                @click="playSong(item.speakId, item.url)"
              ></i>
              <!-- <Icon
            class="mdplay"
            type="md-play"
            v-show="imgPk !== item.speakId"
            @click="playSong(index)"
            />-->
            </div>
          </div>
        </el-col>
      </el-row>
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
    },
    typesSound: {
      type: Object,
      default: {}
    },
    switchTab: {
      type: String,
      default: ''
    },
    curkey: {
      type: String,
      default: ''
    },
  },
  data () {
    return {
      songUrl: null,
      radio: null,
      imgPk: "",
      soundData: [
        { label: "女声1", speakId: "szj", url: "/static/szj.wav" },
        { label: "女声2", speakId: "mh", url: "/static/mh.wav" },
        { label: "女声3", speakId: "yq", url: "/static/yq.wav" },
        { label: "女声4", speakId: "tz", url: "/static/tz.wav" },
        { label: "女声5", speakId: "lcl", url: "/static/lcl.wav" },
        { label: "女声6", speakId: "chm", url: "/static/chm.wav" },
        { label: "女声7", speakId: "sy", url: "/static/sy.wav" },
        { label: "女声8", speakId: "ss", url: "/static/sy.wav" },
      ]
    };
  },
  mounted () {
    // console.log(this.typesSound);
    const key = Object.keys(this.typesSound)[0],
      speakId = this.typesSound[key].speakId
    this.checkBoxPick(speakId)
  },
  methods: {
    checkBoxPick (speakId) {
      this.radio = speakId;
      // console.log(this.radio);
      this.$emit('modelChange', this.radio)
    },
    playSong (speakId, url = '/static/szj.wav') {
      this.imgPk = speakId;
      this.$nextTick(() => {
        this.songUrl = url;
        this.$refs.player.playSong();
      });
    },
    playStatus (val) {
      if (val === 'end') {
        this.imgPk = "";
      }
    }
  },
  components: {
    player
  },
  watch: {
    switchTab (val) {
      if(val === this.curkey){
        const key = Object.keys(this.typesSound)[0]
        const speakId = this.typesSound[key].speakId
        this.checkBoxPick(speakId)
      }
    }
  }
};
</script>

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
      // width: 25%;
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
