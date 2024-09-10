<template>
  <div class="color-type">
    <!-- <span class="name">
      {{ name }}
    </span> -->
    <input
      v-model="modelColor"
      class="value"
      @focus="handleFocus"
      @blur="handleBlur"
    />
  </div>
</template>

<script lang="ts">
import { computed, defineComponent } from 'vue'

export default defineComponent({
  props: {
    name: {
      type: String,
      default: '',
    },
    color: {
      type: String,
      default: '',
    },
  },
  emits: ['inputColor', 'inputFocus', 'inputBlur'],
  setup(props, { emit }) {
    const modelColor = computed({
      get() {
        return props.color || ''
      },
      set(val) {
        emit('inputColor', val)
      },
    })

    // Functions for handling focus events
    const handleFocus = (event: FocusEvent) => {
      emit('inputFocus', event)
    }
    const handleBlur = (event: FocusEvent) => {
      emit('inputBlur', event)
    }
    return {
      modelColor,
      handleFocus,
      handleBlur,
    }
  },
})
</script>

<style lang="scss">
.color-type {
  display: flex;
  font-size: 12px;
  height: 100%;
  flex: 1;
  background: #2e333a;
  .value {
    width: 100%;
    height: 100%;
    padding: 0 6px;
    border: 0;
    color: #fff;
    font-size: 0.7rem;
    background: transparent;
    box-sizing: border-box;
    display: flex;
    &:active,
    &:focus {
      outline: none;
    }
  }
}
</style>
