<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue'
import { injectScrollAreaRootContext } from './ScrollAreaRoot.vue'
import ScrollAreaScrollbarAuto, { type ScrollAreaScrollbarAutoProps } from './ScrollAreaScrollbarAuto.vue'
import { Presence } from '@/Presence'
import { useForwardRef } from '@/shared'

export interface ScrollAreaScrollbarHoverProps extends ScrollAreaScrollbarAutoProps {}
defineProps<ScrollAreaScrollbarHoverProps>()

const rootContext = injectScrollAreaRootContext()

const forwardRef = useForwardRef()

let timeout: ReturnType<typeof setTimeout> | undefined | number
const visible = ref(false)

function handlePointerEnter() {
  window.clearTimeout(timeout)
  visible.value = true
}
function handlePointerLeave() {
  timeout = window.setTimeout(() => {
    visible.value = false
  }, rootContext.scrollHideDelay.value)
}

onMounted(() => {
  const scrollArea = rootContext.scrollArea.value

  if (scrollArea) {
    scrollArea.addEventListener('pointerenter', handlePointerEnter)
    scrollArea.addEventListener('pointerleave', handlePointerLeave)
  }
})

onUnmounted(() => {
  const scrollArea = rootContext.scrollArea.value
  if (scrollArea) {
    window.clearTimeout(timeout)
    scrollArea.removeEventListener('pointerenter', handlePointerEnter)
    scrollArea.removeEventListener('pointerleave', handlePointerLeave)
  }
})
</script>

<script lang="ts">
export default {
  inheritAttrs: false,
}
</script>

<template>
  <Presence :present="forceMount || visible">
    <ScrollAreaScrollbarAuto
      v-bind="$attrs"
      ref="forwardRef"
      :data-state="visible ? 'visible' : 'hidden'"
    >
      <slot />
    </ScrollAreaScrollbarAuto>
  </Presence>
</template>
