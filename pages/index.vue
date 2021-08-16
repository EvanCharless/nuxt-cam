<template lang="pug">
  div
    button(
      v-if="!showCam"
      @click="showCam = true"
    ) Camera
    Camera(
      v-if="showCam"
      captureBtnContent="Capture"
      cancelBtnContent="Retake"
      doneBtnContent="Use Image"
      @back="hideCamera"
      @input="doneCapture"
    )
</template>

<script>
import Camera from "../components/Camera.vue";
export default {
  components: {
    Camera
  },
  data() {
    return {
      showCam: false
    };
  },
  methods: {
    doneCapture(picture) {
      console.log(picture, "picture");
    },
    hideCamera() {
      this.showCam = false;
    }
  }
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
}
</style>
