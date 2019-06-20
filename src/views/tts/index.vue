<template>
  <div class="tts" v-loading.fullscreen.lock='ajaxLoading' element-loading-background="rgba(0, 0, 0, 0.8)">
    <el-row :gutter="10">
      <el-col :xs="24" :sm="16">
        <div class="content">
          <div class="contentInput">
            <el-input
              type="textarea"
              :rows="textareaRows"
              placeholder="请输入内容"
              v-model="textarea"
              maxlength="100"
            ></el-input>
            <div class="counter">
              (
              <span class="textStyle">{{ this.textarea.length }}</span
              >/100字)
            </div>
          </div>

          <el-tabs v-model="activeName" @tab-click="handleClick(activeName)">
            <el-tab-pane
              v-for="(item, key) in moduleList"
              :key="key"
              :label="item.name"
              :name="key"
            >
              <dialect
                :typesSound="item.display_info"
                :curkey="key"
                :switchTab="switchTab"
                :songVolume="songVolume"
                :songRate="songRate"
                @modelChange="modelChange"
              ></dialect>
            </el-tab-pane>
          </el-tabs>
          <el-row :gutter="20">
            <el-col :xs="24" :sm="12">
              <h3>音量</h3>
              <el-slider
                v-model="volume"
                :format-tooltip="formatVolumeToolTip"
                class="slider"
              ></el-slider>
            </el-col>
            <el-col :xs="24" :sm="12">
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
          <player
            :songUrl="songUrl"
            :songVolume="songVolume"
            :songRate="songRate"
            :playing="playing"
            :showControls="true"
            @playStatus="playStatus"
            @changeVolume="changeVolume"
          ></player>
          <el-row :gutter="20">
            <el-col :xs="24" :sm="4">
              <el-button
                style="min-width:100px;width:100%;padding-left: 0px;"
                type="primary"
                :loading="ajaxLoading"
                @click="play()"
              >
                {{ playText }}
                <!-- <Icon :type="playIcon"/> -->
              </el-button>
            </el-col>
          </el-row>
        </div>
      </el-col>
      <el-col :xs="24" :sm="8">
        <div class="operating">
          <h3>用法</h3>
          <ul>
            <li>
              <span>数 字</span> : &nbsp;
              <p>不需要加标记符号</p>
            </li>
            <li>
              <span>电话号码</span> : &nbsp;
              <p>(#83278311)注意里面的1读作幺</p>
            </li>
            <li>
              <span>按位号码</span> : &nbsp;
              <p>($32673)按数字读法 2 读作 二，1 读作 一</p>
            </li>
            <li>
              <span>两读法</span> : &nbsp;
              <p>(@32673)按数字读法 2 读作 两</p>
            </li>
            <li>
              <span></span> &nbsp;&nbsp;&nbsp;
              <p>(!32673)按数字读法 2 读作 两，1 读作 幺</p>
            </li>
            <li>
              <span>注 意</span> : &nbsp;
              <p>
                只支持汉字、数字、字母、逗号、句号、问号以及上面自定义的输入规则，其他的特殊字符都是非法的
              </p>
            </li>
          </ul>
          <p></p>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'
import request from '@/utils/request'

import dialect from "@/components/tts/dialect";
import player from "@/components/tts/dialect/audio";
import { debug } from 'util';
export default {
  data () {
    return {
      switchTab: '',
      ajaxLoading: false,
      songUrl: "",
      songStatus: 'end',
      textarea: "",
      activeName: 'CPU',
      speechRate: 50,
      volume: 50,
      playing: false,
      moduleList: {},
      textareaRows: 15,
      isChange: false,
      model: 'szj',
    };
  },
  computed: {
    playIcon () {
      return this.playing ? "md-pause" : "md-play";
    },
    playText () {
      if (this.isChange || this.songStatus === 'end') {
        return "立即播放"
      } else if (this.playing) {
        return "暂停播放"
      } else {
        return "继续播放"
      }
    },
    songVolume () {
      return this.volume / 100;
    },
    songRate () {
      return (this.speechRate + 50) / 100;
    }
  },
  mounted () {
    const winWidth = document.body.clientWidth
    if (winWidth < 768) {
      this.textareaRows = 8
    }
    request.get("/display/info").then(res => {
      this.moduleList = res.data
      this.activeName = Object.keys(this.moduleList)[0]
      // console.log(res.data);
    })
  },
  methods: {
    async play () {
      // console.log(this.activeName);
      let flag = true;
      if (!this.textarea) {
        this.$notify({
          title: '警告',
          message: '请输入要合成语音的文字',
          type: 'warning'
        });
        return;
      }
      if (this.isChange) {
        this.isChange = false
        flag = await this.synthesis()
        this.playing = true;
        // flag && (this.isChange = false)
      } else if (flag) {
        this.playSound()
      }
    },
    playSound () {
      this.playing = !this.playing;
    },
    handleClick (activeName) {
      this.switchTab = activeName;
    },
    formatVolumeToolTip (index) {
      return "音量条: " + index;
    },
    formatSpeechRateToolTip (index) {
      return "语速: " + (index + 50) / 100;
    },
    playStatus (status) {
      this.songStatus = status
      if (status === 'end') {
        this.playing = false;
        this.songEnd = true;
      } else if (status === 'pause') {
        this.playing = false;
      } else if (status === 'play') {
        this.playing = true;
      }
    },
    changeVolume (volume) {
      // console.log(volume);
      this.volume = volume * 100
    },
    async synthesis () {
      // const url = 'http://192.168.1.166:8080/synthesize'
      const url = '/synthesize';
      console.log(this.model, this.activeName);

      const data = {
        version: this.activeName,
        text: this.textarea.replace(/\n/g, ""),
        task_id: `taskid${Date.now()}`,
        model: this.model,
        volume: this.volume || 50,
        speed: this.songRate || 1
      }
      try {
        this.ajaxLoading = true;
        const blob = await request.post(url, data, {
          responseType: 'blob'
        })
        this.ajaxLoading = false;
        // console.log(blob);
        this.songUrl = URL.createObjectURL(blob.data)
        if (blob.data.type === 'audio/wav') {
          return true;
        } else {
          this.$notify.error({
            title: '错误',
            message: '请输入正确的文本语句'
          });
          return false
        }
      } catch (error) {
        this.ajaxLoading = false;
        this.$notify.error({
          title: '错误',
          message: '请输入正确的文本语句'
        });
        return false
      }
    },
    modelChange (val) {
      this.model = val;
    }
  },
  watch: {
    textarea (newV, oldVal) {
      if (newV !== oldVal) {
        this.isChange = true;
        this.playing = false;
      }
    },
    activeName (newV, oldVal) {
      if (newV !== oldVal) {
        this.isChange = true;
        this.playing = false;
      }
    },
    model (newV, oldVal) {
      if (newV !== oldVal) {
        this.isChange = true;
        this.playing = false;
      }
    },
  },
  components: {
    dialect,
    player
  }
};
</script>

<style lang="scss" scoped>
.tts {
  margin: 20px;
  // display: flex;
  .content {
    // flex: 3;
    // width: 600px;
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
    // flex: 2;
    padding-left: 20px;
    @media screen and (max-width: 1000px) {
      padding: 20px 0 0 0;
    }
    h3 {
      font-weight: 700;
    }
    ul {
      li {
        font-weight: 600;
        margin: 15px 0;
        display: flex;
        span {
          display: inline-block;
          width: 80px;
          text-align: justify;
          text-justify: distribute-all-lines; /*ie6-8*/
          text-align-last: justify; /* ie9*/
          -moz-text-align-last: justify; /*ff*/
          -webkit-text-align-last: justify; /*chrome 20+*/
          &:after {
            content: ".";
            width: 100%;
            display: inline-block;
            overflow: hidden;
            height: 0;
          }
        }

        p {
          flex: 1;
          margin-left: 5px;
        }
      }
    }
  }
}
</style>