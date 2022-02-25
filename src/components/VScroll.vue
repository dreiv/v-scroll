<script setup lang="ts">
import { ref } from "vue";
import { requestAnimationFrame } from "@/helpers";
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
const beforeHeight = ref(0);
const afterHeight = ref(totalHeight - items.value.length * itemHeight);

const onScroll = requestAnimationFrame(({ target: { scrollTop } }: any) => {
  const from = Math.floor((scrollTop - toleranceHeight) / itemHeight);
  const offset = amount + 2 * tolerance;
  items.value = props.getData(from, offset);
  beforeHeight.value = Math.max(from * itemHeight, 0);
  afterHeight.value = Math.max(totalHeight - (from + offset) * itemHeight, 0);
});
</script>

<template>
  <div
    ref="viewport"
    :class="$style.viewport"
    :style="{ height: `${viewportHeight}px` }"
    @scroll.passive="onScroll"
  >
    <div :style="{ height: `${beforeHeight}px` }" />
    <div
      :style="{ height: `${itemHeight}px` }"
      v-for="{ index, text } in items"
      :key="index"
    >
      {{ text }}
    </div>
    <div :style="{ height: `${afterHeight}px` }" />
  </div>
</template>

<style lang="scss" module>
.viewport {
  overflow: auto;
}
</style>
