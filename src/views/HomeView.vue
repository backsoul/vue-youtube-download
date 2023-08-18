<template>
  <div class="container">
    <div
      class="loading"
      style="
        width: 99vw;
        height: 100%;
        position: absolute;
        top: 0px;
        left: 0px;
        right: 0px;
        bottom: 0px;
        background: black;
        opacity: 0.8;
        transition: 1s all;
      "
      v-if="loading"
    ></div>
    <h1
      style="font-size: 2rem; margin: 1rem; color: rgb(177, 177, 177); font-weight: 600"
    >
      Download video youtube from url
    </h1>
    <input
      type="text"
      name="videoUrl"
      v-model="videoUrl"
      style="
        border-radius: 1rem;
        padding: 1rem;
        font-size: 1rem;
        border: none;
        color: rgb(161, 161, 161);
      "
    />
    <button
      type="submit"
      @click="uploadUrl"
      style="
        box-sizing: border-box;
        margin: 0;
        font-weight: normal;
        margin-top: 1rem;
        padding: 1rem;
        border-radius: 1rem;
        border: none;
        background: white;
        color: black;
        font-size: 1rem;
        cursor: pointer;
      "
    >
      Descargar video
    </button>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      percentage: 0,
      videoUrl: "https://www.youtube.com/watch?v=-2EpHyA5tM4",
      loading: false,
    };
  },
  mounted() {},
  methods: {
    async uploadUrl() {
      try {
        this.loading = true;
        const responseVideo = await axios.post("http://localhost:3000/uploadVideo", {
          videoUrl: this.videoUrl,
          nameVideo: this.modifyYouTubeURL(),
        });
        this.downloadVideo();
      } catch (error) {
        console.error("Error al obtener el porcentaje:", error);
      }
    },
    async downloadVideo() {
      try {
        const response = await axios.get(
          `http://localhost:3000/videos/${this.modifyYouTubeURL()}`,
          {
            responseType: "blob",
          }
        );
        const videoBlob = new Blob([response.data], { type: "video/mp4" });
        const link = document.createElement("a");
        link.href = URL.createObjectURL(videoBlob);
        link.download = this.videoUrl.replace("https://www.", "") + ".mp4";
        document.body.appendChild(link);
        link.click();
        window.URL.revokeObjectURL(link.href);
        this.loading = false;
      } catch (error) {
        console.error("Error downloading the video:", error);
      }
    },
    modifyYouTubeURL() {
      let url = this.videoUrl;
      if (url.includes("youtube.com/watch?v=")) {
        url = url.replace("https://", "").replace("http://", "");
        url = url.replace("www.", "").replace("youtube.com/watch?v=", "");
        url = url.replace(/-/g, "").replace("?", "").replace(/&/g, "");
        return url;
      } else {
        return "Invalid YouTube URL format";
      }
    },
  },
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
}
main {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
}
</style>
