<template>
  <div class="slide-container">
    <div class="arrows-wrapper">
      <button :disabled="startTime || activeSlideIndex === 1" @click="handleSwipe(false)">Left</button>
      <button :disabled="startTime || activeSlideIndex === 10" @click="handleSwipe(true)">Right</button>
      <span>{{activeSlideIndex}}</span>
    </div>
    <div ref="wrapper" class="wrapper">
      <div v-for="i in 15" :ref="`slide${i}`" :key="i" :class="`slide color-${i}`" />
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      activeSlideIndex: 1,
      swipeDuration: 500, // ms
      startTime: null,
      slideStep: 150
    }
  },
  mounted () {
    this.calculateSlidesPositions(this.swipeDuration)
  },
  methods: {
    handleSwipe(swipeRight) {
      this.changeIndex(swipeRight)
      this.scrollToNextSlide()
      // this.$refs.wrapper.scrollLeft = this.activeSlideIndex * 150 - 150
    },
    changeIndex (swipeRight) {
      if (swipeRight) {
        if (this.activeSlideIndex === 10) {
          return
        }

        ++this.activeSlideIndex

        return
      }

      if (this.activeSlideIndex === 1) {
        return
      }

      --this.activeSlideIndex
    },
    getSliderStyle(i) {
      if (i > 10) {
        return 'backround-color: rgba(0,0,0,0);'
      }

      let style = `transform: scale(1, ${1 - Math.abs(this.activeSlideIndex - i)/10}; z-index:${10 - Math.abs(this.activeSlideIndex - i)};`

      if (this.activeSlideIndex > i) {
        style += `transform: translate(-400px, 0);`
      }

      return style
    },
    scrollToNextSlide() {
      window.requestAnimationFrame(this.calculateCurrentSwiperPosition)
    },
    calculateCurrentSwiperPosition(timestamp) {
      if (!this.startTime) {
        this.startTime = timestamp
      }

      const timeElapsed = timestamp - this.startTime

      this.calculateSlidesPositions(timeElapsed)
      this.$refs.wrapper.scrollLeft = this.activeSlideIndex * 150 + 150 * (timeElapsed / this.swipeDuration) - 300
      timeElapsed < this.swipeDuration ? window.requestAnimationFrame(this.calculateCurrentSwiperPosition) : this.finishedSwipe()
    },
    finishedSwipe () {
      this.$refs.wrapper.scrollLeft = this.activeSlideIndex * 150 - 150
      this.startTime = null
      return 2
    },
    calculateSlidesPositions(timeElapsed) {
      const currentTimestampScale = timeElapsed / this.swipeDuration > 1 ? 1 : timeElapsed / this.swipeDuration

      for (let i = 1; i < 11; i++) {
        if (this.activeSlideIndex > i) {
          if (this.activeSlideIndex - i === 1) {
            this.$refs[`slide${i}`][0].style.transform = `translate(-${600 * currentTimestampScale}px, 0) scale(1, 1)`;
            continue
          }

          this.$refs[`slide${i}`][0].style.transform = `translate(-800px, 0) scale(1, 1)`;
          continue
        }

        const slidesAwayFromFirst = i - this.activeSlideIndex
        const timestampHeightScale = 1 - slidesAwayFromFirst / 5 - 0.2 + 0.2 * currentTimestampScale // (1 - slidesAwayFromFirst / 5) * currentTimestampScale

        this.$refs[`slide${i}`][0].style.transform = `scale(${timestampHeightScale})`;
      }
    }
  }
}
</script>
<style>
.wrapper {
  width: 500px;
  height: 400px;
  background-color: rgb(240, 255, 240);
  padding: 1em;
  overflow-x: scroll;
  display: flex;
}
.slide {
  width: 400px;
  min-width: 400px;
  height: 300px;
  display: flex;
  margin-right: -250px;
}
.color-1 {
  background-color: green;
  z-index: 10;
}
.color-2 {
  background-color: red;
  z-index: 9;
}
.color-3 {
  background-color: blue;
  z-index: 8;
}
.color-4 {
  background-color: yellow;
  z-index: 7;
}
.color-5 {
  background-color: black;
  z-index: 6;
}
.color-6 {
  background-color: green;
  z-index: 5;
}
.color-7 {
  background-color: red;
  z-index: 4;
}
.color-8 {
  background-color: blue;
  z-index: 3;
}
.color-9 {
  background-color: yellow;
  z-index: 2;
}
.color-10 {
  background-color: black;
  z-index: 1;
}
.color-11
.color-12
.color-13
.color-14
.color-15
.color-16 {
  background-color: rgba(0,0,0,0);
}
</style>
