<template>
  <view class="carousel-3d-container" v-if="list.length > 0">
    <view
      class="carousel-3d-slider"
      @touchstart="touchstart"
      @touchmove.stop="touchmove"
      @touchend="touchend"
    >
      <view
        v-for="(item, i) in list"
        :class="[
          'item',

          isNextPossible && i == 0 ? 'next' : '',
          isPrevPossible && i == total - 1 ? 'prev' : '',
          currentIndex === i ? 'current' : '',
          currentIndex === i - 1 ? 'next' : '',
          currentIndex === i + 1 ? 'prev' : '',
        ]"
        :key="i"
        @click="goActivity(item, i)"
      >
        <image
          :class="['img']"
          :src="item.image || ''"
          :alt="item.title || ''"
        />
      </view>
    </view>
  </view>
</template>

<script>
export default {
  props: {
    minSwipeDistance: {
      type: Number,
      default: 10,
    },
    loop: {
      type: Boolean,
      default: true,
    },
    status: {
      type: Boolean,
      default: true,
    },
  },
  name: "List",
  data() {
    return {
      mousedown: false,
      dragStartX: 0,
      dragOffset: 0,
      currentIndex: 0,
      timeout: 0,
      list: [
        {
          image: "/static/example.jpg",
          title: "example",
        },
        {
          image: "/static/example.jpg",
          title: "example",
        },
        {
          image: "/static/example.jpg",
          title: "example",
        },
      ],
    };
  },
  computed: {
    total() {
      return this.list.length;
    },
    isLastSlide() {
      return this.currentIndex === this.total - 1;
    },
    isFirstSlide() {
      return this.currentIndex === 0;
    },
    isNextPossible() {
      return this.loop && this.isLastSlide;
    },
    isPrevPossible() {
      return this.loop && this.isFirstSlide;
    },
  },
  methods: {
    goActivity(activity, i) {
      if (i === this.currentIndex) {
        this.goto(activity);
      } else {
        this.goSlide(i);
        this.autoplay();
      }
    },
    goto(activity) {},
    touchstart(e) {
      if (!e.touches) {
        e.preventDefault();
      }
      this.mousedown = true;
      this.dragStartX = e.touches[0].clientX;
    },
    touchmove(e) {
      if (!this.mousedown) {
        return;
      }
      const eventPosX = e.touches[0].clientX;
      const deltaX = this.dragStartX - eventPosX;
      this.dragOffset = deltaX;
      if (this.dragOffset > this.minSwipeDistance) {
        this.touchend();
        this.goNext();
        this.autoplay();
      } else if (this.dragOffset < -this.minSwipeDistance) {
        this.touchend();
        this.goPrev();
        this.autoplay();
      }
    },
    goNext() {
      this.goSlide(this.currentIndex + 1);
    },
    /**
     * Go to previous slide
     */
    goPrev() {
      this.goSlide(this.currentIndex - 1);
    },
    goSlide(index) {
      if (index < 0 && this.loop) {
        this.currentIndex = this.total - 1;
      } else if (index > this.total - 1 && this.loop) {
        this.currentIndex = 0;
      } else {
        this.currentIndex = index < 0 || index > this.total - 1 ? 0 : index;
      }
    },
    touchend() {
      this.mousedown = false;
      this.dragOffset = 0;
    },
    autoplay() {
      if (this.total > 1) {
        clearInterval(this.timeout);
        this.timeout = setInterval(() => {
          this.goSlide(this.currentIndex + 1);
        }, 3000);
      }
    },
  },
  created() {
    this.autoplay();
  },

  watch: {
    status() {
      if (this.status) {
        this.autoplay();
      } else {
        clearInterval(this.timeout);
      }
    },
  },
};
</script>

<style>
.carousel-3d-container {
  min-height: 1rpx;
  width: 750rpx;
  position: relative;
  z-index: 0;
  overflow: hidden;
  margin: 0 auto;
  box-sizing: border-box;
}
.carousel-3d-slider {
  position: relative;
  margin: 0 auto;
  transform-style: preserve-3d;
  perspective: 1000rpx;
  height: 180rpx;
}
.item {
  position: absolute;
  left: 55rpx;
  width: 640rpx;
  height: 180rpx;
  opacity: 0;
  transform: translateZ(-800rpx);
  transition: transform 500ms ease 0s, opacity 500ms ease 0s,
    visibility 500ms ease 0s;
}

.prev {
  transform: translateX(-134rpx) translateZ(-300rpx);
  opacity: 0.5;
  visibility: visible;
}
.next {
  transform: translateX(134rpx) translateZ(-300rpx);
  opacity: 0.5;
  visibility: visible;
}
.current {
  z-index: 1;
  transform: translateZ(0);
  opacity: 1;
}

.img {
  width: 100%;
  height: 100%;
  border-radius: 10rpx;
}
</style>
