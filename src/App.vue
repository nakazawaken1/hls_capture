<script setup>
import HlsVideo from './components/HlsVideo.vue'
import { ref } from 'vue'

const video = ref()
const image = ref()
const download = () => {
  const a = document.createElement('a')
  a.download = 'capture.png'
  a.href = image.value.src
  a.click()
}
</script>

<template>
  <h1>HLSライブ動画のキャプチャ</h1>
  <article>
    <section>
      <HlsVideo ref="video" src="https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8" autoplay playsinline muted crossorigin="anonymous" />
      <button type="button" @click="video.capture(url => image.src = url)">キャプチャ</button>
    </section>
    <section>
      <img ref="image" />
      <button type="button" @click="download">ダウンロード</button>
    </section>
  </article>
</template>

<style>
h1 {
  text-align: center;
}
article {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
}
section {
  display: flex;
  gap: 10px;
  flex-direction: column;
  justify-content: center;
}
video, img {
  width: 684px;
  height: 360px;
  border: 1px solid silver;
}
</style>
