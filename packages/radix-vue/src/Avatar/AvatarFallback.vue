<script lang="ts">
export interface AvatarFallbackProps extends PrimitiveProps {
  delayMs?: number
}
</script>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { Primitive, type PrimitiveProps } from '../Primitive'
import { injectAvatarRootContext } from './AvatarRoot.vue'

const props = withDefaults(defineProps<AvatarFallbackProps>(), {
  delayMs: 0,
  as: 'span',
})

const rootContext = injectAvatarRootContext()

const canRender = ref(false)
let timeout: ReturnType<typeof setTimeout> | undefined

watch(rootContext.imageLoadingStatus, (value) => {
  if (value === 'loading') {
    canRender.value = false
    if (props.delayMs) {
      timeout = setTimeout(() => {
        canRender.value = true
        clearTimeout(timeout)
      }, props.delayMs)
    }
    else {
      canRender.value = true
    }
  }
}, { immediate: true })
</script>

<template>
  <Primitive
    v-if="canRender && rootContext.imageLoadingStatus.value !== 'loaded'"
    :as-child="props.asChild"
    :as="as"
  >
    <slot />
  </Primitive>
</template>
