<template>
  <section class="v-slider-wrap">
    <div class='v-slider' ref="slider">
      <div class='v-slider-slides'>
        <slot></slot>
      </div>
    </div>
    <div v-if="pagination && swipe && getNumSlides() > 1" class='v-slider-pagination'>
      <i v-for="(e,i) in getNumSlides()"
        class="v-slider-dot"
        :key="i"
        :class="{active: curIndex === i}"
        :style="[paginationStyle, curIndex === i ? paginationActiveStyle : '']"
        @click="slide(i)"></i>
    </div>
  </section>
</template>

<script>
import swipe from './swipe.js'

export default {
  name: 'v-slider',

  props: {
    /**
     * startSlide Integer (default:0) -> index position Swipe should start at
     * speed Integer (default:300) -> speed of prev and next transitions in milliseconds.
     * auto Integer -> begin with auto slideshow (time in milliseconds between slides)
     * continuous Boolean (default:true) -> create an infinite feel with no endpoints
     * disableScroll Boolean (default:false) -> stop any touches on this container from scrolling the page
     * stopPropagation Boolean (default:false) -> stop event propagation
     * callback Function -> runs at slide change.
     * transitionEnd Function -> runs at the end slide transition.
     *
     * {
     *   startSlide: 2,
     *   speed: 400,
     *   auto: 3000,
     *   continuous: true,
     *   disableScroll: false,
     *   stopPropagation: false,
     *   callback: function(index, elem) {},
     *   transitionEnd: function(index, elem) {}
     * }
     *
     * 已知 bug 解决：只有两个 slide 请主动关闭 continuous: false
     */
    options: {
      type: Object,
      default: () => ({})
    },
    pagination: {
      type: Boolean,
      default: true
    },
    paginationStyle: Object,
    paginationActiveStyle: Object,
  },

  data() {
    return {
      swipe: null,
      curIndex: 0
    };
  },

  mounted() {
    this.init();
  },

  beforeDestroy() {
    this.swipe.kill();
    this.swipe = null;
  },

  methods: {
    init() {
      this.swipe = new swipe(this.$refs.slider, Object.assign({
        callback: this.change.bind(this),
        transitionEnd: this.transitionEnd.bind(this)
      }, this.options));
      this.$emit('init', this.swipe);
    },

    change(index, elem) {
      this.curIndex = index;
      this.$emit('change', index, elem);
    },

    transitionEnd(index, elem) {
      this.$emit('transitionEnd', index, elem);
    },

    slide(index, duration) {
      if (!this.swipe) return;
      this.swipe.slide(index, duration);
    },

    next () {
      if (!this.swipe) return;
      this.swipe.next();
    },

    prev () {
      if (!this.swipe) return;
      this.swipe.prev();
    },

    setup() {
      if (!this.swipe) return;
      this.swipe.setup();
    },

    // 取得当前位置
    getPos() {
      if (!this.swipe) return;
      return this.swipe.getPos();
    },

    // 取得总数
    getNumSlides() {
      if (!this.swipe) return;
      return this.options.slideLength || this.swipe.getNumSlides();
    },
  },
};
</script>

<style>
.v-slider-wrap .v-slider {
  overflow: hidden;
  visibility: hidden;
  position: relative;
}
.v-slider-wrap .v-slider .v-slider-slides {
  overflow: hidden;
  position: relative;
  display: flex;
  width: 100%;
  height: 100%;
}
.v-slider-wrap .v-slider .v-slider-slides > div {
  position: relative;
  display: inline-block;
}
.v-slider-wrap .v-slider-pagination {
  position: relative;
  height: rem(100);
  display: flex;
  align-items: center;
  justify-content: center;
}
.v-slider-wrap .v-slider-pagination .v-slider-dot {
  display: inline-block;
  width: rem(14);
  height: rem(14);
  margin: 0 rem(12);
  border-radius: 50%;
  background: #b4b4b4;
  cursor: pointer;
}
.v-slider-wrap .v-slider-pagination .v-slider-dot.active {
  background: #ff7178;
}
</style>
