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
const offsetHeight = ref(0);

const onScroll = requestAnimationFrame(({ target: { scrollTop } }: any) => {
  from.value = Math.floor((scrollTop - toleranceHeight) / itemHeight);
  const offset = amount.value + 2 * tolerance;

  items.value = props.getData(from.value, offset);
  offsetHeight.value = Math.max(from.value * itemHeight, 0);
});

const onResize = debounced(() => {
  const height = viewport.value.clientHeight;
  amount.value = Math.floor(height / itemHeight);

  items.value = props.getData(from.value, amount.value);
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
          v-for="{ index, text } in items"
          :key="index"
        >
          {{ text }}
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
