<script lang="ts">
export interface SliderRangeProps extends PrimitiveProps {}
</script>

<script setup lang="ts">
import { computed } from 'vue'
import { Primitive, type PrimitiveProps } from '@/Primitive'
import { injectSliderRootContext } from './SliderRoot.vue'
import { convertValueToPercentage, injectSliderOrientationContext } from './utils'

withDefaults(defineProps<SliderRangeProps>(), { as: 'span' })
const rootContext = injectSliderRootContext()
const orientation = injectSliderOrientationContext()

const percentages = computed(() => rootContext.modelValue?.value?.map(value =>
  convertValueToPercentage(value, rootContext.min.value, rootContext.max.value),
))

const offsetStart = computed(() => rootContext.modelValue!.value!.length > 1 ? Math.min(...percentages.value!) : 0)
const offsetEnd = computed(() => 100 - Math.max(...percentages.value!))
</script>

<template>
  <Primitive
    :data-disabled="rootContext.disabled.value"
    :data-orientation="rootContext.orientation.value"
    :as-child="asChild"
    :as="as"
    :style="{
      [orientation!.startEdge]: `${offsetStart}%`,
      [orientation!.endEdge]: `${offsetEnd}%`,
    }"
  >
    <slot />
  </Primitive>
</template>
