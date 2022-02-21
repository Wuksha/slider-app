<template>
  <div class="app-container">
    <Slider />
    <!-- <SliderV2 /> -->
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
}
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  width: 100vw;
  height: 100vh;
}
.app-container {
  width: 100%;
  height: 100%;
  padding: 50px;
  display: flex;
  justify-content: space-around;
}
</style>