<template>
    <div class="arco-select-wrapper" @click="toggleDropdown" ref="selectRef">
        <div class="arco-select" :class="{ 'arco-select-active': isOpen }">
            <span class="arco-select-value">{{ selectedLabel }}</span>
            <span class="arco-select-arrow">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="arco-icon"
                    :class="{ 'arco-icon-rotate': isOpen }">
                    <path fill="currentColor"
                        d="M12 15.25a.74.74 0 01-.53-.22l-5-5A.75.75 0 017.53 9L12 13.44 16.47 9a.75.75 0 011.06 1l-5 5a.74.74 0 01-.53.25z">
                    </path>
                </svg>
            </span>
        </div>
        <transition name="fade">
            <ul v-if="isOpen" class="arco-select-dropdown">
                <li v-for="option in options" :key="option.value" @click.stop="selectOption(option)"
                    class="arco-select-option" :class="{ 'arco-select-option-selected': option.value === modelValue }">
                    {{ option.label }}
                </li>
            </ul>
        </transition>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, watch, PropType } from 'vue'

interface Option {
    label: string
    value: string | number
}

export default defineComponent({
    name: 'ArcoSelect',
    props: {
        options: {
            type: Array as PropType<Option[]>,
            required: true
        },
        placeholder: {
            type: String,
            default: '请选择'
        },
        modelValue: {
            type: [String, Number],
            default: ''
        }
    },
    emits: ['update:modelValue'],
    setup(props, { emit }) {
        const isOpen = ref(false)
        const selectRef = ref<HTMLElement | null>(null)

        const selectedLabel = computed(() => {
            const selected = props.options.find(option => option.value === props.modelValue)
            return selected ? selected.label : props.placeholder
        })

        const toggleDropdown = () => {
            isOpen.value = !isOpen.value
        }

        const selectOption = (option: Option) => {
            emit('update:modelValue', option.value)
            isOpen.value = false
        }

        const handleClickOutside = (event: MouseEvent) => {
            if (selectRef.value && !selectRef.value.contains(event.target as Node)) {
                isOpen.value = false
            }
        }

        watch(() => props.modelValue, (newValue) => {
            if (newValue === '') {
                // 如果值被清空，可以在这里添加额外的逻辑
            }
        })

        // 生命周期钩子
        const mounted = () => {
            document.addEventListener('click', handleClickOutside)
        }

        const unmounted = () => {
            document.removeEventListener('click', handleClickOutside)
        }

        return {
            isOpen,
            selectRef,
            selectedLabel,
            toggleDropdown,
            selectOption,
            mounted,
            unmounted
        }
    }
})
</script>

<style lang="scss">
.arco-select-wrapper {
    position: relative;
    width: 38px;

    .arco-select {
        display: flex;
        align-items: center;
        justify-content: space-between;
        min-height: 25px;
        padding: 0 4px;
        background-color: #48505a;
        color: #e5e6eb;
        cursor: pointer;
        transition: all 0.1s cubic-bezier(0, 0, 1, 1);
    }

    .arco-select-value {
        font-size: 12px;
        color: #e5e6eb;
        transform: scale(0.6);
        transform-origin: left;
        width: 20px;
    }

    .arco-select-arrow {
        display: flex;
        align-items: center;
    }

    .arco-icon {
        width: 12px;
        height: 12px;
        transition: transform 0.2s;

        &-rotate {
            transform: rotate(180deg);
        }
    }

    .arco-select-dropdown {
        position: absolute;
        top: 100%;
        left: 0;
        width: 100%;
        margin-top: 4px;
        padding: 4px 0;
        background-color: #48505a;
        border-radius: 2px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        z-index: 1000;
        list-style: none;
    }

    .arco-select-option {
        padding: 2px;
        font-size: 12px;
        color: #e5e6eb;
        cursor: pointer;
        transition: background-color 0.1s;
        transform: scale(0.7);

        &-selected {
            font-weight: 500;
        }
    }

    .fade-enter-active,
    .fade-leave-active {
        transition: opacity 0.2s ease;
    }

    .fade-enter-from,
    .fade-leave-to {
        opacity: 0;
    }
}
</style>