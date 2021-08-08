<template>
  <video-swiper
      :video-list="videoList"
      @swiperchange="onSwiperChange"
  ></video-swiper>
</template>

<script>
import { reactive, toRefs } from 'vue';
import VideoSwiper from "../components/VideoSwiper.vue";
import videoList from "../static/videoList.json";

export default {
  name: "ShortVideo",
  components: {
    [VideoSwiper.name]: VideoSwiper,
  },
  setup() {
    const state = reactive({
      videoList: [],
    })

    initData()

    // 初始化数据
    function initData() {
      setTimeout(()=> {
        state.videoList = videoList
      }, 300)
    }

    function onSwiperChange(realIndex, activeIndex) {
      console.log(realIndex, activeIndex)
    }

    return {
      ...toRefs(state),
      onSwiperChange,
    }
  }
}
</script>

<style scoped>

</style>