<template>
  <div class="container">
    <div class="container-left">
      <h3>Camera Preview</h3>
      <div class="container-left_head">
        <video style="width: 400rpx; height: 400rpx"></video>
        <div class="overlay">
          <p>{{ message }}</p>
        </div>
      </div>
      <div class="container-left_operator">
        <el-input
          class="input"
          v-model="inputWords"
          placeholder="请输入内容"
        ></el-input>
        <el-button type="primary" round @click="showWords">显示文字</el-button>
        <el-button round type="danger" @click="clearWords">清除文字</el-button>
        <el-button
          type="primary"
          icon="el-icon-scissors"
          @click="snapVideo"
          round
          >截图</el-button
        >
      </div>
      <!-- <div @click="openVideo">开启摄像头</div> -->
      <!-- <div @click="closeVideo">关闭摄像头</div> -->
    </div>
    <div class="container-right" id="container-right">
      <h3>Snapshots</h3>
      <div class="container-right_list" id="canvas_list"></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      video: null,
      MediaStreamTrack: null,
      message: "",
      inputWords: "",
      shotList: [],
    };
  },
  methods: {
    // 打开摄像头
    openVideo() {
      let that = this;
      this.video = document.querySelector("video");

      if (window.navigator.webkitGetUserMedia) {
        window.navigator.webkitGetUserMedia(
          {
            audio: true,
            video: true,
          },
          (stream) => {
            this.MediaStreamTrack =
              typeof stream.stop === "function"
                ? stream
                : stream.getTracks()[1];

            //把摄像头捕捉到的数据给视频
            that.video.srcObject = stream;
            // _.video.src = window.URL.createObjectURL(stream);
            that.video.play();

            console.log(stream, this.MediaStreamTrack);
          },
          (err) => {
            console.log("err>>>", err);
          }
        );
      }
    },
    // 截取图像
    snapVideo() {
      let myCanvas = document.createElement("canvas");
      myCanvas.width = 200;
      myCanvas.height = 150;
      let context = myCanvas.getContext("2d");
      // let context = document.querySelector("canvas").getContext("2d");
      this.shotList = [...this.shotList, this.video];
      context.drawImage(this.video, 0, 0, 200, 150);
      document.getElementById("canvas_list").appendChild(myCanvas);
    },
    // 关闭摄像头
    closeVideo() {
      this.MediaStreamTrack && this.MediaStreamTrack.stop();
      this.MediaStreamTrack = null;
    },
    // 显示文字
    showWords() {
      this.message = this.inputWords;
    },
    // 清除文字
    clearWords() {
      this.message = "";
      this.inputWords = "";
    },
  },
  mounted() {
    this.openVideo();
  },
};
</script>
<style lang="scss" scoped>
.container {
  display: flex;
  padding: 20px;
  video {
    position: relative;
    z-index: 0;
  }
}
.container-left {
  min-width: 60%;
  overflow: auto;
  &_head {
    position: relative;
    .overlay {
      position: absolute;
      bottom: 20px;
      left: 10%;
      z-index: 1;
      p {
        margin: 0px;
        line-height: 60px;
        color: aquamarine;
        font-size: 60px;
      }
    }
  }
  &_operator {
    margin: 20px;
    white-space: nowrap;
  }
  .input {
    width: 300px;
    margin-right: 15px;
  }
}
.container-right {
  width: 30%;
  padding-left: 30px;
  // min-height: 500px;
  // overflow: auto;
  &_list {
    height: 500px;
    overflow: auto;
  }
  canvans {
    margin-bottom: 15px;
  }
}
</style>
