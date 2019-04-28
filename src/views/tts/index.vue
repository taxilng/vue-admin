<style lang="scss" scoped>
.tts {
  margin: 20px;
  display: flex;
  .content {
    flex: 3;
    width: 600px;
    .contentInput {
      position: relative;

      /deep/ .el-textarea__inner {
        resize: none;
        background: #283041;
        color: #ffffff;
        padding: 20px;
      }
      .counter {
        position: absolute;
        bottom: 0;
        right: 0;
        color: #858a94;
        padding: 0 30px 20px 0;
        .textStyle {
          color: #3080c1;
        }
      }
    }
    .control {
      display: flex;
      .voluem {
        flex: 1;
      }
    }
    /deep/ .el-button.el-button--primary {
      padding-right: 10px;
      i {
        margin-left: 10px;
      }
    }
  }
  .operating {
    flex: 2;
    padding-left: 20px;
    h3 {
      font-weight: 700;
    }
    ul {
      li {
        font-weight: 600;
        margin: 10px 0;
        span {
          font-weight: 600;
          display: inline-block;
          width: 65px;
          text-align: justify;
          text-justify: distribute-all-lines; /*ie6-8*/
          text-align-last: justify; /* ie9*/
          -moz-text-align-last: justify; /*ff*/
          -webkit-text-align-last: justify; /*chrome 20+*/
        }
      }
    }
  }
}
</style>

<template>
  <div class="tts">
    <player
      :songUrl="songUrl"
      :songVolume="songVolume"
      :songRate="songRate"
      :playing="playing"
      @playend="playend"
    ></player>
    <div class="content">
      <div class="contentInput">
        <el-input type="textarea" rows="15" placeholder="请输入内容" v-model="textarea" maxlength="100"></el-input>
        <div class="counter">
          (
          <span class="textStyle">{{this.textarea.length}}</span>/100字)
        </div>
      </div>

      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane
          v-for="item in moduleList"
          :key="item.name"
          :label="item.label"
          :name="item.name"
        >
          <dialect :songVolume="songVolume" :songRate="songRate"></dialect>
        </el-tab-pane>
      </el-tabs>
      <el-row :gutter="20">
        <el-col :span="12">
          <h3>音量</h3>
          <el-slider v-model="volume" :format-tooltip="formatVolumeToolTip" class="slider"></el-slider>
        </el-col>
        <el-col :span="12">
          <h3>语速</h3>
          <el-slider
            v-model="speechRate"
            :format-tooltip="formatSpeechRateToolTip"
            :step="10"
            show-stops
            class="slider"
          ></el-slider>
        </el-col>
      </el-row>
      <el-button type="primary" @click="playSound()">
        {{playText}}
        <!-- <Icon :type="playIcon"/> -->
      </el-button>
    </div>
    <div class="operating">
      <h3>用法</h3>
      <ul>
        <li>
          <span>数 字</span> : &nbsp;不需要加标点符号
        </li>
        <li>
          <span>电话号码</span> : &nbsp;(#83278311)注意里面的「1」读作「幺」
        </li>
        <li>
          <span>按位号码</span> : &nbsp;($32673)按数字读法「2」 读作「二」，「1」 读作「一」
        </li>
        <li>
          <span>2两读法</span> : &nbsp;(@32673)按数字读法「2」 读作「两」
        </li>
        <li>
          <span>2两读法</span> : &nbsp;(!32673)按数字读法「2」 读作「两」，「1」 读作「幺」
        </li>
        <li>
          <span>注 意</span> : &nbsp;合成支持英文字母，数字，汉字组合
        </li>
      </ul>
      <p></p>
    </div>
  </div>
</template>

<script>
import dialect from "@/components/tts/dialect";
import player from "@/components/tts/dialect/audio";
export default {
  data() {
    return {
      songUrl: null,
      songEnd: false,
      textarea: "",
      activeName: null,
      speechRate: 50,
      volume: 50,
      playing: false,
      moduleList: [
        { name: "1", label: "模型1" },
        { name: "2", label: "模型2" },
        { name: "3", label: "模型3" },
        { name: "4", label: "模型4" },
        { name: "5", label: "模型5" },
        { name: "6", label: "模型6" },
        { name: "7", label: "模型7" },
        { name: "8", label: "模型8" },
        { name: "9", label: "模型9" }
      ]
    };
  },
  computed: {
    playIcon() {
      return this.playing ? "md-pause" : "md-play";
    },
    playText() {
      return this.playing ? "暂停播放" : "立即播放";
    },
    songVolume() {
      return this.volume / 100;
    },
    songRate() {
      return (this.speechRate + 50) / 100;
    }
  },
  mounted() {
    setTimeout(() => {
      this.activeName = this.moduleList[0].name;
    }, 100);
    this.songUrl = require("@/static/szj.wav");
  },
  methods: {
    playSound() {
      this.playing = !this.playing;
    },
    handleClick(tab, event) {
      console.log(tab, event);
    },
    formatVolumeToolTip(index) {
      return "音量条: " + index;
    },
    formatSpeechRateToolTip(index) {
      return "语速: " + (index + 50) / 100;
    },
    playend() {
      this.playing = false;
      this.songEnd = true;
    }
  },
  watch: {
  },
  components: {
    dialect,
    player
  }
};
</script>

