<script setup lang="ts">
import { Ref, ref } from "vue";
import VScroll from "./components/VScroll.vue";

const latency = () => Math.random() * 500 + 300; // simulate network latency of 300-800ms

const count = 500;
const data: any[] = Array.from({ length: 500 }, (_, index) => ({ key: index }));
const items: Ref<any[]> = ref([]);

let timeout: any;
function requestItems(offset: number, limit: number) {
  clearTimeout(timeout);
  const start = Math.max(0, offset);
  const end = Math.min(offset + limit, count);
  items.value = data.slice(start, end);

  timeout = setTimeout(() => {
    for (let i = offset; i < offset + limit; i++) {
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
