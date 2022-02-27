<script setup lang="ts">
import { Ref, ref, computed } from "vue";
import { Direction } from "./components/types";
import VScroll from "./components/VScroll.vue";

const grouped = ref(true);

const additional = 100;
const count = 10000;
const data: any[] = Array.from({ length: count }, (_, index) => ({
  loading: true,
  key: index,
}));
const items: Ref<any[]> = ref([]);

let timeout: any;
function loadMore(start: number, end: number, direction: Direction) {
  timeout = setTimeout(() => {
    let from = start,
      to = end;
    if (direction === "down") {
      to = Math.min(end + additional, count);
    } else {
      from = Math.max(0, start - additional);
    }

    for (let i = from; i < to; i++) {
      delete data[i].loading;
      data[i].group = Math.floor(i / 25); // simulate grouping
      data[i].text = `item ${i}`;
    }

    items.value = data.slice(start, end);
  }, Math.random() * 500 + 300);
}

function requestItems(offset: number, limit: number, direction: Direction) {
  clearTimeout(timeout);

  const start = Math.max(0, offset);
  const end = Math.min(offset + limit, count);
  items.value = data.slice(start, end);

  if (items.value.find(({ loading }) => loading)) {
    loadMore(start, end, direction);
  }
}

const maybeGroupedItems = computed(() =>
  grouped.value
    ? items.value.map((item, index) => ({
        ...item,
        ...(!item.loading &&
          item.group !== items.value[index - 1]?.group && { isHeader: true }),
      }))
    : items.value
);
</script>

<template>
  <label>Grouped? <input type="checkbox" v-model="grouped" /></label>
  <hr />
  <v-scroll
    :count="count"
    :request-items="requestItems"
    :items="maybeGroupedItems"
    :itemHeight="20"
    v-slot="{ item: { loading, text, group, isHeader, itemHeight } }"
  >
    <div
      v-if="isHeader"
      :style="{ height: `${itemHeight}px` }"
      :class="$style.header"
    >
      <div>group {{ group }}</div>
    </div>
    <div :style="{ height: `${itemHeight}px` }">
      <template v-if="loading">loading... 🌌</template>
      <template v-else>{{ text }}</template>
    </div>
  </v-scroll>
</template>

<style lang="scss" module>
.header {
  position: sticky;
  top: 0;

  background-color: lightyellow;
}
</style>
