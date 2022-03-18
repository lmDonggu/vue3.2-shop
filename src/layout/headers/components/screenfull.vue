<template>
  <div @click="handleFullScreen" id="screenFull">
    <svg-icon :icon="icon ? 'exit-fullscreen' : 'fullscreen'"></svg-icon>
  </div>
</template>

<script setup>
import { onBeforeMount, onMounted, ref } from 'vue'
import screenfull from 'screenfull'

const icon = ref(screenfull.isFullscreen)
const handleFullScreen = () => {
  if (screenfull.isEnabled) {
    screenfull.toggle()
  }
}

const changeIcon = () => {
  icon.value = screenfull.isFullscreen
}
onMounted(() => {
  screenfull.on('change', changeIcon)
})
onBeforeMount(() => {
  screenfull.off('change')
})
</script>

<style lang="scss" scoped></style>
