<template lang="pug">
  div
    section.camera-container(
      v-if="isValid"
      :style="styling"
      class="photo-capture"
    )
      //- .upload-images__header
      //-   button(
      //-     @click="back"
      //-   ) Back
      //-   .description
      //-     h2 For best results
      //-     h2
      //-       | Focus&nbsp;
      //-       span Image&nbsp;
      //-       | In Center
      //-   i Info
      div
        video(v-show="showVideo" ref="player" class="camera" autoplay playsinline)
        canvas(v-show="!showVideo" class="preview" ref="canvas")
      .upload-images__controls(v-if="!hideBtns" class="center photo-capture-actions")
        button.upload-inages__button(
          :class="'btn flex-center '  + buttonsClasses"
          @click.prevent="capture"
          v-if="showVideo"
        ) {{captureBtnContent}}

        div(v-else)
          button.upload-inages__button(:class="'btn '+ buttonsClasses" @click.prevent="cancel") {{cancelBtnContent}}
          button.upload-inages__button(:class="'btn '+ buttonsClasses" @click.prevent="done") {{doneBtnContent}}

    section.not-avaiable__page
      h1 Sorry, Only available at mobile version
</template>

<script>
export default {
  name: "PhotoCapture",
  props: {
    hideBtns: {
      type: Boolean,
      isRequired: false,
      default: false
    },
    styling: {
      type: Object,
      isRequired: false
    },
    hideButtons: {
      type: Boolean,
      default: false
    },
    buttonsClasses: {
      type: String,
      default: ""
    },
    captureBtnContent: {
      default: "Capture"
    },
    cancelBtnContent: {
      default: "Cancel"
    },
    doneBtnContent: {
      default: "OK"
    }
  },
  data() {
    return {
      showVideo: true,
      picture: null,
      isValid: true
    };
  },
  mounted() {
    this.videoPlayer = this.$refs.player;
    this.canvasElement = this.$refs.canvas;
    this.streamUserMediaVideo();
  },
  methods: {
    streamUserMediaVideo() {
      if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
        return;
      }
      navigator.mediaDevices
        .getUserMedia({ video: true })
        .then(stream => (this.videoPlayer.srcObject = stream))
        .catch(() => {
          this.isValid = false;
        });
    },
    capture() {
      this.showVideo = false;
      this.canvasElement.width = this.videoPlayer.videoWidth;
      this.canvasElement.height = this.videoPlayer.videoHeight;
      var context = this.canvasElement.getContext("2d");
      context.translate(this.canvasElement.width, 0);
      context.scale(-1, 1);
      context.drawImage(this.$refs.player, 0, 0);
      this.stopVideoStream();
      this.picture = this.$refs.canvas.toDataURL();
    },
    done() {
      this.$emit("input", this.picture);
      this.showVideo = true;
      this.streamUserMediaVideo();
    },
    cancel() {
      this.showVideo = true;
      this.streamUserMediaVideo();
    },
    stopVideoStream() {
      if (!(this.$refs.player && this.$refs.player.srcObject)) return;
      this.$refs.player.srcObject.getVideoTracks().forEach(track => {
        track.stop();
      });
    }
  }
};
</script>

<style lang="scss">
.camera-container {
  display: none;

  @include small-potrait() {
    display: block;
  }

  .camera,
  .preview {
    width: 100%;
    height: 100%;
    transform: scaleX(-1);
  }

  .upload-images__controls {
    background: #0a1f44;
    color: #fff;
    padding: 48px 0;
    display: flex;
    justify-content: center;

    .upload-inages__button {
      padding: 20px 55px;
      margin: 0 8px;
      font-size: 18px;
    }
  }
}

.not-avaiable__page {
  display: block;

  @include small-potrait() {
    display: none;
  }
}
</style>
