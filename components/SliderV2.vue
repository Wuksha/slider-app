<template>
  <div class="slide-container">
    <div class="arrows-wrapper">
      <button :disabled="startTime || activeSlideIndex === 1" @click="handleSwipe(false)">Left</button>
      <button :disabled="startTime || activeSlideIndex === 10" @click="handleSwipe(true)">Right</button>
      <span>{{activeSlideIndex}}</span>
    </div>
    <div class="arrows-wrapper">
      <button @click="activeBezierSet = 1">Scroll 1</button>
      <button @click="activeBezierSet = 2">Scroll 2</button>
      <button @click="activeBezierSet = 3">Scroll 3</button>
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
      slideStep: 150,
      activeBezierSet: 1,
      bezierPointsSet1: [0, 0.05, 0.2, 1],
      bezierPointsSet2: [0, 0.7, 0.95, 1],
      bezierPointsSet3: [0, 0.33, 0.67, 1],
    }
  },
  mounted () {
    this.calculateSlidesPositions(1)
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


      let bezierPoints
      if (this.activeBezierSet === 1) {
        bezierPoints = this.bezierPointsSet1
      } else if (this.activeBezierSet === 2) {
        bezierPoints = this.bezierPointsSet2
      } else {
        bezierPoints = this.bezierPointsSet3
      }

      const t = timeElapsed / this.swipeDuration
      const x1 = bezierPoints[0]
      const x2 = bezierPoints[1]
      const x3 = bezierPoints[2]
      const x4 = bezierPoints[3]
      const point1 = Math.pow((1 - t), 3) * x1
      const point2 = Math.pow((1 - t), 2) * 3 * t * x2
      const point3 = Math.pow(t, 2) * 3 *(1 - t) * x3
      const point4 = Math.pow(t, 3) * x4

      const bezierEquation = point1 + point2 + point3 + point4

      this.$refs.wrapper.scrollLeft = this.activeSlideIndex * this.slideStep + this.slideStep * bezierEquation - this.slideStep * 2
      this.calculateSlidesPositions(bezierEquation)

      if (timeElapsed < this.swipeDuration) {
        window.requestAnimationFrame(this.calculateCurrentSwiperPosition)
      } else {
        this.finishedSwipe()
      }
    },
    finishedSwipe () {
      this.$refs.wrapper.scrollLeft = this.activeSlideIndex * this.slideStep - this.slideStep
      this.startTime = null
      return 2
    },
    calculateSlidesPositions(bezierEquation) {
      for (let i = 1; i < 11; i++) {
        if (this.activeSlideIndex > i) {
          if (this.activeSlideIndex - i === 1) {
            this.$refs[`slide${i}`][0].style.transform = `translate(-${600 * bezierEquation}px, 0) scale(1, 1)`;
            continue
          }

          this.$refs[`slide${i}`][0].style.transform = `translate(-800px, 0) scale(1, 1)`;
          continue
        }

        const slidesAwayFromFirst = i - this.activeSlideIndex
        const timestampHeightScale = 1 - slidesAwayFromFirst / 5 - 0.2 + 0.2 * bezierEquation // (1 - slidesAwayFromFirst / 5) * bezierEquation

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
