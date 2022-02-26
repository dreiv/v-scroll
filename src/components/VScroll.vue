<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { requestAnimationFrame, debounced } from "@/helpers";

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

const onScroll = requestAnimationFrame(({ target: { scrollTop } }: any) => {
  from.value = Math.floor((scrollTop - toleranceHeight) / itemHeight);
  const offset = amount.value + 2 * tolerance;

  props.requestItems(from.value, offset);
  offsetHeight.value = Math.max(from.value * itemHeight, 0);
});

const onResize = debounced(() => {
  amount.value = Math.floor(viewport.value.clientHeight / itemHeight);

  props.requestItems(from.value, amount.value);
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
      <div :style="{ transform: `translateY(${offsetHeight}px)` }">
        <div
          :style="{ height: `${itemHeight}px` }"
          v-for="item in items"
          :key="item.key"
        >
          <slot :item="item" />
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" module>
.viewport {
  overflow: auto;
  height: 100px;

  resize: vertical;
}
</style>
