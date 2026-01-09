<script setup>
import Main from './components/Main.vue'
 
</script>

<template>
  <Main />
</template>

<style scoped>

</style>

<style>
@import url('https://fonts.googleapis.com/css?family=Google+Sans|Noto+Sans|Castoro&display=swap');

body {
  margin: 0;
  padding: 0;
  background: #fff;
  min-height: 100vh;
  font-family: 'Noto Sans', sans-serif;
}

#app {
  background: #fff;
  min-height: 100vh;
}

/* Fix el-image preview */
.el-image-viewer__wrapper {
  z-index: 9999 !important;
  position: fixed !important;
  top: 0 !important;
  left: 0 !important;
  width: 100vw !important;
  height: 100vh !important;
}

.el-image-viewer__mask {
  opacity: 1 !important;
  position: absolute !important;
  top: 0 !important;
  left: 0 !important;
  width: 100% !important;
  height: 100% !important;
}

.el-image-viewer__canvas {
  position: absolute !important;
  top: 50% !important;
  left: 50% !important;
  transform: translate(-50%, -50%) !important;
  display: flex !important;
  justify-content: center !important;
  align-items: center !important;
}

.el-image-viewer__img {
  max-width: 90vw !important;
  max-height: 90vh !important;
  object-fit: contain !important;
}
</style>
