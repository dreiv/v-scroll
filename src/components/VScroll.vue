<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { requestAnimationFrame, debounced } from "@/helpers";

const props = defineProps<{
  count: number;
  getData: Function;
}>();

const { count } = props;
const itemHeight = 20;
const tolerance = 2;

const from = ref(0);
const viewport = ref();
const amount = ref();
const items = ref([]);
const toleranceHeight = tolerance * itemHeight;

const totalHeight = count * itemHeight;
const beforeHeight = ref(0);
const afterHeight = ref(totalHeight - items.value.length * itemHeight);

const onScroll = requestAnimationFrame(({ target: { scrollTop } }: any) => {
  from.value = Math.floor((scrollTop - toleranceHeight) / itemHeight);
  const offset = amount.value + 2 * tolerance;

  items.value = props.getData(from.value, offset);
  beforeHeight.value = Math.max(from.value * itemHeight, 0);
  afterHeight.value = Math.max(totalHeight - (from.value + offset) * itemHeight, 0);
});

const onResize = debounced(() => {
  const height = viewport.value.clientHeight;
  amount.value = Math.floor(height / itemHeight);

  items.value = props.getData(from.value, amount.value);
})
const resizeObserver = new ResizeObserver(onResize);

onMounted(() => {
  resizeObserver.observe(viewport.value);
});

onUnmounted(() => {
  resizeObserver.disconnect();
})
</script>

<template>
  <div
    ref="viewport"
    :class="$style.viewport"
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
  height: 100px;

  resize: vertical;
}
</style>
