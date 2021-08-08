<template>
  <short-video></short-video>
</template>

<script>
import ShortVideo from "./pages/ShortVideo";

export default {
  name: 'App',
  components: {
    ShortVideo
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
