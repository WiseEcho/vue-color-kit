<template>
  <div class="page">
    <div class="bg" :style="{ background: color }">
      <div class="title">vue-color-cmstop</div>
      <div class="cover">
        <color-picker
          :theme="theme"
          :color="color"
          :sucker-hide="false"
          @changeColor="changeColor"
          @openSucker="openSucker"
        />
        <img ref="cover" />
      </div>
    </div>
    <div class="github">
      <a href="https://github.com/anish2690/vue-color-cmstop" target="_blank">
        Fork me on GitHub
      </a>
    </div>
    <div
      class="switch"
      :class="{ 'anim-pull': inAnimation }"
      @animationend="animationEnd"
      @click="changeTheme"
    />
  </div>
</template>

<script>
import imgCover from './cover.jpg'
import { ColorPicker } from '/@/'
export default {
  components: {
    ColorPicker,
  },
  data() {
    return {
      color: '#1BC7B1',
      suckerCanvas: null,
      suckerArea: [],
      isOpenSucker: false,
      theme: '',
      inAnimation: false,
    }
  },
  mounted() {
    const cover = this.$refs.cover
    cover.setAttribute('crossorigin', 'Anonymous')
    cover.width = '800'
    cover.src = imgCover
  },
  methods: {
    changeColor(color) {
      const { r, g, b, a } = color.rgba
      this.color = `rgba(${r}, ${g}, ${b}, ${a})`
    },
    openSucker(isOpen) {},
    changeTheme() {
      this.theme = this.theme ? '' : 'light'
      this.inAnimation = true
    },
    animationEnd() {
      this.inAnimation = false
    },
  },
}
</script>

<style lang="scss">
html,
body {
  height: 100%;
}

body {
  margin: 0;
}
.page {
  height: 100vh;
  .bg {
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    .title {
      color: #fff;
      font-size: 48px;
      text-shadow: 0 0 8px rgba(0, 0, 0, 0.16);
    }
    .cover {
      display: flex;
      justify-content: space-around;
      align-items: center;
      width: 100%;
    }
  }
  .github {
    position: fixed;
    right: 0;
    top: 0;
    width: 200px;
    height: 30px;
    line-height: 30px;
    font-size: 14px;
    font-weight: bold;
    background: #a00;
    text-align: center;
    box-shadow: 0 0 8px 0 rgba(0, 0, 0, 0.3);
    transform: rotate(45deg) translateX(60px);
    a {
      display: block;
      text-decoration: none;
      color: #fff;
    }
  }
  .switch {
    position: fixed;
    left: 20%;
    top: -100px;
    width: 2px;
    height: 300px;
    background: #ddd;
    cursor: pointer;
    &::after {
      content: '';
      position: absolute;
      left: -10px;
      bottom: -20px;
      width: 22px;
      height: 22px;
      border: 2px solid #ddd;
      border-radius: 50%;
      box-sizing: border-box;
    }
  }
}
@keyframes line {
  0% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(100px);
  }
  100% {
    transform: translateY(0);
  }
}
.anim-pull {
  animation: line 0.6s;
}
</style>
