<script setup lang="ts">
import { Ref, ref } from "vue";
import { Direction } from "./components/types";
import VScroll from "./components/VScroll.vue";

const latency = () => Math.random() * 500 + 300; // simulate network latency of 300-800ms

const extraItems = 100;
const count = 5000;
const data: any[] = Array.from({ length: count }, (_, index) => ({ key: index }));
const items: Ref<any[]> = ref([]);

let timeout: any;
function requestItems(offset: number, limit: number, direction: Direction) {
  clearTimeout(timeout);
  const start = Math.max(0, offset);
  const end = Math.min(offset + limit, count);
  items.value = data.slice(start, end);

  timeout = setTimeout(() => {
    let from = start,
      to = end;
    if (direction === "down") {
      to = Math.min(end + extraItems, count);
    } else {
      from = Math.max(0, offset - extraItems);
    }

    for (let i = from; i < to; i++) {
      data[i].text = `item ${i}`;
    }

    items.value = data.slice(start, end);
  }, latency());
}
</script>

<template>
  <v-scroll
    :count="count"
    :request-items="requestItems"
    :items="items"
    :itemHeight="20"
    v-slot="{ item: { text } }"
  >
    <template v-if="!text">loading...</template>
    <template v-else>{{ text }}</template>
  </v-scroll>
</template>
