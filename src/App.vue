<script setup lang="ts">
import { Ref, ref, computed } from "vue";
import { Direction } from "./components/types";
import VScroll from "./components/VScroll.vue";

const latency = () => Math.random() * 500 + 300; // simulate network latency of 300-800ms

const extraItems = 100;
const count = 10000;
const data: any[] = Array.from({ length: count }, (_, index) => ({
  key: index,
}));
const items: Ref<any[]> = ref([]);

let timeout: any;
function loadMore(start: number, end: number, direction: Direction) {
  timeout = setTimeout(() => {
    let from = start,
      to = end;
    if (direction === "down") {
      to = Math.min(end + extraItems, count);
    } else {
      from = Math.max(0, start - extraItems);
    }

    for (let i = from; i < to; i++) {
      data[i].group = Math.floor(i / 25);
      data[i].text = `item ${i}`;
    }

    items.value = data.slice(start, end);
  }, latency());
}

function requestItems(offset: number, limit: number, direction: Direction) {
  clearTimeout(timeout);
  const start = Math.max(0, offset);
  const end = Math.min(offset + limit, count);
  items.value = data.slice(start, end);

  if (items.value.find(({ text }) => !text)) {
    loadMore(start, end, direction);
  }
}

const groupedItems = computed(() =>
  items.value.map((item, index) => ({
    ...item,
    ...(item.text &&
      item.group !== items.value[index - 1]?.group && { isHeader: true }),
  }))
);
</script>

<template>
  <v-scroll
    :count="count"
    :request-items="requestItems"
    :items="groupedItems"
    :itemHeight="20"
    v-slot="{ item: { text, group, isHeader, itemHeight } }"
  >
    <div
      v-if="isHeader"
      :style="{ height: `${itemHeight}px` }"
      :class="$style.header"
    >
      <div>group {{ group }}</div>
    </div>
    <div :style="{ height: `${itemHeight}px` }">
      <template v-if="!text">🌌loading...</template>
      <template v-else>{{ text }}</template>
    </div>
  </v-scroll>
</template>

<style lang="scss" module>
.header {
  position: sticky;
  top: 0;

  background-color: lightblue;
}
</style>
