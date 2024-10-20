<template>
  <div class="camera-container">
    <video id="video" ref="video" playsinline class="video-preview"></video>
    <canvas ref="canvas" class="photo-canvas"></canvas>
    <div class="controls">
      <button @click="startCamera">开启摄像头</button>
      <button @click="takePhoto" :disabled="!stream">拍照</button>
      <button @click="stopCamera" :disabled="!stream">关闭摄像头</button>
    </div>
    <img :src="photo" alt="Captured photo" v-if="photo" class="photo-preview" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      photo: null,
      stream: null, 
    };
  },
  methods: {
    async startCamera() {
      try {
        this.stream = await navigator.mediaDevices.getUserMedia({ video: true });
        const video = this.$refs.video;
        video.srcObject = this.stream;
        video.play();
      } catch (error) {
        console.error("无法访问摄像头", error);
        alert("摄像头访问失败，请检查权限设置。");
      }
    },
    takePhoto() {
      const video = this.$refs.video;
      const canvas = this.$refs.canvas;
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      const context = canvas.getContext("2d");

      context.drawImage(video, 0, 0, canvas.width, canvas.height);

      this.photo = canvas.toDataURL("image/png");
    },
    stopCamera() {
      if (this.stream) {
        this.stream.getTracks().forEach(track => track.stop());
        this.stream = null;
      }
    }
    },
  beforeDestroy() {
    this.stopCamera();
  },
  mounted() {
    /* eslint-disable */
      document.addEventListener("WeixinJSBridgeReady", () => {
      const video = document.getElementById("video");
      video.play();
      });
    /* eslint-enable */
    }
};
</script>

<style scoped>
.camera-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  align-items: center;
  padding: 20px;
  background-color: #f0f4f8;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.video-preview {
  width: 100%;
  max-width: 500px;
  margin-bottom: 15px;
  border: 2px solid #ccc;
  object-fit: cover;
}

.controls {
  display: flex;
  justify-content: space-around;
  width: 100%;
  max-width: 500px;
  margin-top: 10px;
}

button {
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:disabled {
  background-color: #6c757d;
}

button:hover:not(:disabled) {
  background-color: #0056b3;
}

.photo-preview {
  width: 100%;
  max-width: 500px;
  margin-top: 15px;
  border-radius: 8px;
  border: 2px solid #007bff;
  object-fit: cover;
}

.photo-canvas {
  display: none;
}
</style>
