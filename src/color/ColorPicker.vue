<template>
  <div class="hu-color-picker" :class="{ light: isLightTheme }" :style="{ width: totalWidth + 'px' }">
    <div class="color-set">
      <Saturation ref="saturation" :color="rgbString" :hsv="hsv" :size="hueHeight"
        @selectSaturation="selectSaturation" />
      <Hue ref="hue" :hsv="hsv" :width="hueWidth" :height="hueHeight" @selectHue="selectHue" />
      <Alpha ref="alpha" :color="rgbString" :rgba="rgba" :width="hueWidth" :height="hueHeight"
        @selectAlpha="selectAlpha" />
    </div>
    <div :style="{ height: previewHeight + 'px' }" class="color-show">
      <Preview :color="rgbaString" :width="previewWidth" :height="previewHeight" />
    </div>
    <div class="box-wrap" style="margin-top: 8px;">
      <MSelect :options="modelOptions" v-model="showModel"></MSelect>
      <Box name="HEX" v-show="showModel === 'HEX'" :color="modelHex" @inputColor="inputHex" @inputFocus="handleFocus"
        @inputBlur="handleBlur" />
      <Box name="RGBA" v-show="showModel === 'RGBA'" :color="modelRgba" @inputColor="inputRgba"
        @inputFocus="handleFocus" @inputBlur="handleBlur" />
      <Sucker v-if="!suckerHide" @openSucker="openSucker" @selectSucker="selectSucker" />
      <div class="reset-btn" @click="reset">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-rotate-ccw reset"><path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
      </div>
    </div>
    <Colors ref="colorsRef" :color="rgbaString" :colors-default="colorsDefault" :colors-history-key="colorsHistoryKey"
      @selectColor="selectColor" />
    <!-- custom options -->
    <slot></slot>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { setColorValue, rgb2hex } from './composible'

import Saturation from './Saturation.vue'
import Hue from './Hue.vue'
import Alpha from './Alpha.vue'
import Preview from './Preview.vue'
import Sucker from './Sucker.vue'
import Box from './Box.vue'
import Colors from './Colors.vue'
import MSelect from './Select.vue'

export default defineComponent({
  components: {
    Saturation,
    Hue,
    Alpha,
    Preview,
    Sucker,
    Box,
    Colors,
    MSelect,
  },
  emits: ['changeColor', 'openSucker', 'inputFocus', 'inputBlur'],
  props: {
    color: {
      type: String,
      default: '#000000',
    },
    theme: {
      type: String,
      default: 'dark',
    },
    suckerHide: {
      type: Boolean,
      default: true,
    },
    colorsDefault: {
      type: Array,
      default: () => [
        '#00B42A',
        '#3C7EFF',
        '#FF7D00',
        '#F76965',
        '#F7BA1E',
        '#F5319D',
        '#D91AD9',
        '#9FDB1D',
        '#FADC19',
        '#722ED1',
        '#3491FA',
        '#7BE188',
        '#93BEFF',
        '#FFCF8B',
        '#FBB0A7',
        '#FCE996',
        '#FB9DC7',
        '#F08EE6',
        '#C396ED',
        '#9FD4FD',

      ],
    },
    colorsHistoryKey: {
      type: String,
      default: 'vue-colorpicker-history',
    },
  },
  data() {
    return {
      hueWidth: 15,
      hueHeight: 152,
      previewHeight: 5,
      showModel: 'HEX',
      modelOptions: [{ label: 'HEX', value: 'HEX' }, { label: 'RGBA', value: 'RGBA' }],
      modelRgba: '',
      modelHex: '',
      r: 0,
      g: 0,
      b: 0,
      a: 1,
      h: 0,
      s: 0,
      v: 0,
    }
  },
  computed: {
    isLightTheme(): boolean {
      return this.theme === 'light'
    },
    totalWidth(): number {
      return this.hueHeight + (this.hueWidth + 8) * 2
    },
    previewWidth(): number {
      return this.totalWidth
    },
    rgba(): object {
      return {
        r: this.r,
        g: this.g,
        b: this.b,
        a: this.a,
      }
    },
    hsv(): object {
      return {
        h: this.h,
        s: this.s,
        v: this.v,
      }
    },
    rgbString(): string {
      return `rgb(${this.r}, ${this.g}, ${this.b})`
    },
    rgbaStringShort(): string {
      return `${this.r}, ${this.g}, ${this.b}, ${this.a}`
    },
    rgbaString(): string {
      return `rgba(${this.rgbaStringShort})`
    },
    hexString(): string {
      return rgb2hex(this.rgba, true)
    },
  },
  created() {
    Object.assign(this, setColorValue(this.color))
    this.setText()

    this.$watch('rgba', () => {
      this.$emit('changeColor', {
        rgba: this.rgba,
        hsv: this.hsv,
        hex: this.modelHex,
      })
    })
    this.oldColor = this.color
  },
  methods: {
    selectSaturation(color: any) {
      const { r, g, b, h, s, v } = setColorValue(color)
      Object.assign(this, { r, g, b, h, s, v })
      this.setText()
    },
    handleFocus(event: FocusEvent) {
      this.$emit('inputFocus', event)
    },
    handleBlur(event: FocusEvent) {
      this.$emit('inputBlur', event)
    },
    selectHue(color: any) {
      const { r, g, b, h, s, v } = setColorValue(color)
      Object.assign(this, { r, g, b, h, s, v })
      this.setText()
      this.$nextTick(() => {
        // @ts-ignore

        this.$refs.saturation.renderColor()
        // @ts-ignore

        this.$refs.saturation.renderSlide()
      })
    },
    selectAlpha(a: any) {
      this.a = a
      this.setText()
    },
    inputHex(color: string) {
      const { r, g, b, a, h, s, v } = setColorValue(color)
      Object.assign(this, { r, g, b, a, h, s, v })
      this.modelHex = color
      this.modelRgba = this.rgbaStringShort
      this.$nextTick(() => {
        // @ts-ignore
        this.$refs.saturation.renderColor()
        // @ts-ignore
        this.$refs.saturation.renderSlide()
        // @ts-ignore
        this.$refs.hue.renderSlide()
      })
    },
    inputRgba(color: string) {
      const { r, g, b, a, h, s, v } = setColorValue(color)
      Object.assign(this, { r, g, b, a, h, s, v })
      this.modelHex = this.hexString
      this.modelRgba = color
      this.$nextTick(() => {
        // @ts-ignore
        this.$refs.saturation.renderColor()
        // @ts-ignore

        this.$refs.saturation.renderSlide()
        // @ts-ignore

        this.$refs.hue.renderSlide()
      })
    },
    setText() {
      this.modelHex = this.hexString
      this.modelRgba = this.rgbaStringShort
    },
    openSucker(isOpen: boolean) {
      this.$emit('openSucker', isOpen)
    },
    selectSucker(color: string) {
      const { r, g, b, a, h, s, v } = setColorValue(color)
      Object.assign(this, { r, g, b, a, h, s, v })
      this.setText()
      this.$nextTick(() => {
        // @ts-ignore

        this.$refs.saturation.renderColor()
        // @ts-ignore

        this.$refs.saturation.renderSlide()

        // @ts-ignore
        this.$refs.hue.renderSlide()
      })
    },
    selectColor(color: string) {
      const { r, g, b, a, h, s, v } = setColorValue(color)
      Object.assign(this, { r, g, b, a, h, s, v })
      this.setText()
      this.$nextTick(() => {
        // @ts-ignore

        this.$refs.saturation.renderColor()
        // @ts-ignore

        this.$refs.saturation.renderSlide()

        // @ts-ignore
        this.$refs.hue.renderSlide()
      })
    },
    setColorsHistory(color: string) {
      this.$refs.colorsRef.setColorsHistory(color)
    },
    reset() {
      Object.assign(this, setColorValue(this.oldColor))
      this.$emit('changeColor', {
        rgba: this.rgba,
        hsv: this.hsv,
        hex: this.modelHex,
      })
    }
  },
})
</script>

<style lang="scss">
.hu-color-picker {
  padding: 10px;
  background: #1d2024;
  border-radius: 4px;
  box-shadow: 0 0 16px 0 rgba(0, 0, 0, 0.16);
  z-index: 1;

  &.light {
    background: #f7f8f9;
    .box-wrap {
      background: #f7f8f9;
    }
    .sucker-wrap {
      background: #eceef0;

      &:hover {
        .sucker {
          background: #e9e9e9;
        }
      }
    }
    .reset-btn {
      background: #eceef0;
      &:hover {
        background: #e9e9e9;
      }
    }

    .title-tip {
      color: #666;
    }

    .arco-select {
      background-color: #e7e8e9;
      color: #666;

      .arco-select-value {
        color: #666;
      }
    }

    .arco-select-dropdown {
      background-color: #e7e8e9;

      .arco-select-option {
        color: #666;
      }
    }

    .color-type {
      .name {
        background: #e7e8e9;
      }

      .value {
        color: #666;
        background: #eceef0;
      }
    }

    .colors.history {
      border-top: 1px solid #eee;
    }
  }

  .reset-btn {
    width: 24px;
    height: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #2e333a;
    &:hover {
      background: #4d535c;
    }
    .reset {
      width: 13px;
      height: 13px;
      color: #999;
      &:hover,
      &.active {
        color: #1593ff;
      }
    }
  }

  .color-set {
    display: flex;
  }

  .color-show {
    margin-top: 8px;
    display: flex;
  }

  .box-wrap {
    display: flex;
    align-items: center;
    height: 25px;
    background: #2e333a;

    select {
      width: 60px;
      height: 100%;
      padding: 0 2px;
      color: #999;
      background: #252930;
      border: none;
      font-size: 12px;
    }
  }
}
</style>
