<script setup lang="ts">
import { ref } from "vue";
import { Options } from "./types";
const props = defineProps<{
  options: Options;
  getData: Function;
}>();

const { count, amount, tolerance } = props.options;

const itemHeight = 20;

const viewport = ref<HTMLDivElement>();
const items = ref(props.getData(1, amount));

const viewportHeight = amount * itemHeight;
const toleranceHeight = tolerance * itemHeight;

const totalHeight = count * itemHeight;
const topPaddingHeight = ref(0);
const bottomPaddingHeight = ref(totalHeight - items.value.length * itemHeight);

function onScroll({ target: { scrollTop } }: any) {
  const index = Math.floor((scrollTop - toleranceHeight) / itemHeight);
  items.value = props.getData(index, amount + tolerance);
  topPaddingHeight.value = Math.max(index * itemHeight, 0);
  bottomPaddingHeight.value = Math.max(
    totalHeight - topPaddingHeight.value - items.value.length * itemHeight,
    0
  );
}
</script>

<template>
  <div
    ref="viewport"
    :class="$style.viewport"
    :style="{ height: `${viewportHeight}px` }"
    @scroll.passive="onScroll"
  >
    <div :style="{ height: `${topPaddingHeight}px` }" />
    <div
      :style="{ height: `${itemHeight}px` }"
      v-for="{ index, text } in items"
      :key="index"
    >
      {{ text }}
    </div>
    <div :style="{ height: `${bottomPaddingHeight}px` }" />
  </div>
</template>

<style lang="scss" module>
.viewport {
  overflow: auto;
}
</style>
