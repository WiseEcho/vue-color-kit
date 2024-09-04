<template>
  <div>
    <svg
      v-if="!isSucking"
      :class="{ active: isOpenSucker }"
      class="sucker"
      xmlns="http://www.w3.org/2000/svg"
      viewBox="-12 -12 48 48"
      @click="openSucker"
    >
      <path
        d="M13.1,8.2l5.6,5.6c0.4,0.4,0.5,1.1,0.1,1.5s-1.1,0.5-1.5,0.1c0,0-0.1,0-0.1-0.1l-1.4-1.4l-7.7,7.7C7.9,21.9,7.6,22,7.3,22H3.1C2.5,22,2,21.5,2,20.9l0,0v-4.2c0-0.3,0.1-0.6,0.3-0.8l5.8-5.8C8.5,9.7,9.2,9.6,9.7,10s0.5,1.1,0.1,1.5c0,0,0,0.1-0.1,0.1l-5.5,5.5v2.7h2.7l7.4-7.4L8.7,6.8c-0.5-0.4-0.5-1-0.1-1.5s1.1-0.5,1.5-0.1c0,0,0.1,0,0.1,0.1l1.4,1.4l3.5-3.5c1.6-1.6,4.1-1.6,5.8-0.1c1.6,1.6,1.6,4.1,0.1,5.8L20.9,9l-3.6,3.6c-0.4,0.4-1.1,0.5-1.5,0.1"
      />
    </svg>
    <svg
      v-if="isSucking"
      class="sucker"
      viewBox="-16 -16 68 68"
      xmlns="http://www.w3.org/2000/svg"
      stroke="#9099a4"
    >
      <g fill="none" fill-rule="evenodd">
        <g transform="translate(1 1)" stroke-width="4">
          <circle stroke-opacity=".5" cx="18" cy="18" r="18" />
          <path d="M36 18c0-9.94-8.06-18-18-18">
            <animateTransform
              attributeName="transform"
              type="rotate"
              from="0 18 18"
              to="360 18 18"
              dur="1s"
              repeatCount="indefinite"
            />
          </path>
        </g>
      </g>
    </svg>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
  data() {
    return {
      isOpenSucker: false, // Whether it is in the straw state
      suckerPreview: null, // Preview color dom next to the straw
      isSucking: false, // Is it in a straw waiting state
    }
  },
  methods: {
    async openSucker() {
      if (!this.isOpenSucker) {
        this.isOpenSucker = true
        this.isSucking = true
        this.$emit('openSucker', this.isOpenSucker)
        if (!('EyeDropper' in window)) return;
        try {
          const eyeDropper = new (window as any).EyeDropper();
          const result = await eyeDropper.open();
          this.$emit('selectSucker', result.sRGBHex)
          this.isOpenSucker = false
          this.isSucking = false
          this.$emit('openSucker', this.isOpenSucker)
        } catch (error) {
          console.error('取色器被取消或发生错误', error);
        }
      } else {
        this.isOpenSucker = false
        this.isSucking = false
        this.$emit('openSucker', this.isOpenSucker)
      }
    },
  },
})
</script>

<style lang="scss">
.sucker {
  width: 30px;
  fill: #9099a4;
  background: #2e333a;
  cursor: pointer;
  transition: all 0.3s;
  &:hover,
  &.active {
    fill: #1593ff;
  }
}
</style>
