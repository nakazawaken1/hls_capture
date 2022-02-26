<template>
  <video ref="video" />
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref, watch } from "vue"
import Hls from "hls.js"

const props = defineProps({
  src: String
})

const video = ref()
const hls = new Hls()
const updateSrc = (src) => {
  hls.detachMedia()
  if (!src) {
    return
  }
  if (video.value.canPlayType && video.value.canPlayType('application/vnd.apple.mpegurl')) {
    video.value.src = src
  } else if (Hls.isSupported()) {
    hls.loadSource(src)
    hls.attachMedia(video.value)
  }
}
const capture = (then, format = 'image/png') => {
  const width = video.value.videoWidth
  const height = video.value.videoHeight
  const canvas = document.createElement('canvas')
  canvas.width = width
  canvas.height = height
  const context = canvas.getContext('2d')
  context.drawImage(video.value, 0, 0, width, height)
  if (context.getImageData(0, 0, 1, 1).data.every(i => i == 0)) {
    navigator.clipboard.read().then(items => {
      const getImage = items.map(i => {
        const type = i.types.find(j => j.startsWith('image'))
        return type && (() => i.getType(type))
      })[0]
      if (getImage) {
        getImage().then(image => then(URL.createObjectURL(image)))
      }
    })
  } else {
    then(canvas.toDataURL(format))
  }
}
defineExpose({ capture })

onMounted(() => updateSrc(props.src))

watch(() => props.src, src => updateSrc(src))

onBeforeUnmount(() => updateSrc())
</script>