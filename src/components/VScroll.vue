<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { requestAnimationFrame, debounced } from "@/helpers";
import type { Direction } from "./types";

const props = defineProps<{
  count: number;
  itemHeight: number;
  requestItems: Function;
  items: any[];
}>();

const { count, itemHeight } = props;
const tolerance = 2;

const viewport = ref();
const from = ref(0);
const amount = ref();
const toleranceHeight = tolerance * itemHeight;

const totalHeight = count * itemHeight;
const offsetHeight = ref(0);

let lastScrollTop = 0;
const onScroll = requestAnimationFrame(({ target: { scrollTop } }: any) => {
  from.value = Math.floor((scrollTop - toleranceHeight) / itemHeight);
  const offset = amount.value + 2 * tolerance;
  const dirrection: Direction = scrollTop > lastScrollTop ? "down" : "up";

  props.requestItems(from.value, offset, dirrection);
  offsetHeight.value = Math.max(from.value * itemHeight, 0);
  lastScrollTop = Math.max(scrollTop, 0); // For Mobile or negative scrolling
});

const onResize = debounced(() => {
  amount.value = Math.floor(viewport.value.clientHeight / itemHeight);

  props.requestItems(from.value, amount.value, "down");
});

const resizeObserver = new ResizeObserver(onResize);
onMounted(() => {
  resizeObserver.observe(viewport.value);
});

onUnmounted(() => {
  resizeObserver.disconnect();
});
</script>

<template>
  <div ref="viewport" :class="$style.viewport" @scroll.passive="onScroll">
    <div :style="{ height: `${totalHeight}px` }">
      <div :style="{ height: `${offsetHeight}px` }" />
      <template v-for="item in items" :key="item.key">
        <slot :item="item" />
      </template>
    </div>
  </div>
</template>

<style lang="scss" module>
.viewport {
  overflow: auto;
  height: 600px;

  resize: vertical;
}
</style>
