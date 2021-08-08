<template>
  <swiper
      class="swipe-wrap"
      :initialSlide="initialSlide"
      :direction="direction"
      :loop="true"
      :allowSlidePrev="allowSlidePrev"
      :allowSlideNext="allowSlideNext"
      @slideChange="swiperChange"
  >
    <swiper-slide v-for="(item) in curQueue" :key="item._id">
      <div class="video-wrap">
        <div class="video-title">{{ item.title }}</div>
        <video
            loop controls
            webkit-playsinline="true"
            x5-video-player-type="h5-page"
            x5-video-player-fullscreen="true"
            playsinline
            preload="meta"
            controlslist="nodownload nofullscreen"
            :src="item.src"
            class="video-content"
        ></video>

          <div class="bottom-down">
            <div class="bd-left"></div>
            <div class="bd-right"> 上划播放 <img src="../assets/arrow-top.png" /></div>
          </div>
      </div>
    </swiper-slide>
  </swiper>
</template>

<script>
import { reactive, toRefs, watch, nextTick } from 'vue'

import { Swiper, SwiperSlide } from 'swiper/vue'
import 'swiper/swiper.min.css'

export default {
  name: 'VideoSwiper',
  components: {
    Swiper,
    SwiperSlide,
  },
  props: {
    videoList: {
      type: Array,
      required: true,
    },
  },
  setup(props, context) {
    const state = reactive({
      // swiper配置
      initialSlide: 2,  // 默认播放位置
      direction: 'vertical',  // 轮播方向
      allowSlidePrev: true,   // 允许划到上一个
      allowSlideNext: true,   // 允许划到下一个
      
      first_load: true, // 第一次加载时会触发swiperChange，加开关控制
      last_index: 1,  // 上一次swiper位置
      all_index: 1,  // allQueue的游标

      curQueue: [], // 当前swiper队列
      allQueue: [], // 全部内容队列
    })

    // 监听videoList变化
    watch(
        () => props.videoList,
        (newVal) => {
          videoListChanged(newVal)
        },
    )

    async function videoListChanged(newVal) {
      if (newVal.length > 0) {
        const allQueue = Array.from(newVal)
        state.allQueue = allQueue
        if (state.curQueue.length === 0) {
          state.curQueue = allQueue.slice(0, 3)
          await nextTick()
          playCurrent(2)
        }
      }
    }

    // 轮播切换事件
    function swiperChange(swiper) {
      const current = swiper.realIndex  // curQueue中真实index

      let { first_load, all_index, last_index, allQueue, curQueue } = state

      // 第一次切换属于数据触发，不作处理
      if (first_load) {
        state.first_load = false
        return
      }

      const diff = current - last_index
      if (diff === 0) return
      context.emit('swiperchange', swiper.realIndex, swiper.activeIndex)

      // 播放切换后的内容
      playCurrent(swiper.activeIndex)

      // 轮播切换已完成，current标记为上一次
      state.last_index = current

      const direction = diff === 1 || diff === -2 ? 'next' : 'prev'

      // 划到下一个
      if (direction === 'next') {
        all_index += 1
        if (all_index < allQueue.length - 1) {
          curQueue[(current + 1) % 3] = allQueue[all_index + 1]
        }
      }
      // 划到上一个
      else if (direction === 'prev') {
        all_index -= 1
        if (all_index > 0) {
          curQueue[(current + 2) % 3] = allQueue[all_index - 1]
        }
      }

      // 设置全局index
      state.all_index = all_index

      // 边界禁止滑动
      if (all_index === 0) {
        state.allowSlidePrev = false
      } else if (all_index === allQueue.length - 1) {
        state.allowSlideNext = false
      } else {
        if (!state.allowSlidePrev) {
          state.allowSlidePrev = true
        }
        if (!state.allowSlideNext) {
          state.allowSlideNext = true
        }
      }

    }

    // 播放
    function playCurrent(current) {
      const nodes = document.getElementsByClassName('video-content')
      Array.from(nodes).forEach(function (ctx, index) {
        if (index !== current) {
          ctx.pause()
        } else {
          ctx.play()
        }
      })
    }

    return {
      ...toRefs(state),
      swiperChange,
    }
  },
}
</script>

<style scoped>
.swipe-wrap {
  width: 100%;
  height: 100%;
}

.video-wrap {
  width: 100%;
  height: 100%;
  position: relative;
  display: flex;
  flex-direction: column;
  background: linear-gradient(#000 0%, #C00 50%, #000 100%);
}

/* 视频标题 */
.video-wrap .video-title {
  position: absolute;
  top: 0;
  padding: 16px 22px;
  text-align: left;
  color: #fff;
  font-size: 20px;
  font-weight: bold;
  line-height: 28px;
}

/* 视频内容 */
.video-wrap .video-content {
  width: 100%;
  height: 100px;
  flex: 1;
}

/* 视频底部 */
.bottom-down {
  height: 34px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background: #000;
}

.bottom-down .bd-left {
  width: 54px;
  height: 32px;
}

.bottom-down .bd-right {
  display: flex;
  color: #fff;
  font-weight: bold;
  font-size: 16px;
}

.bottom-down .bd-right img {
  margin-left: 3px;
  width: 22px;
  height: 22px;
}
</style>
