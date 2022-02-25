<script setup lang="ts">
import { Ref, ref } from "vue";
import VScroll from "./components/VScroll.vue";

const latency = () => Math.random() * 500 + 300; // simulate network latency of 300-800ms

const count = 500;
const data = Array(500).fill({ loading: true });
const items: Ref<any[]> = ref([]);

let timeout: any;
function getData(offset: number, limit: number) {
  clearTimeout(timeout);
  const start = Math.max(0, offset);
  const end = Math.min(offset + limit, count);
  items.value = data.slice(start, end);

  timeout = setTimeout(() => {
    for (let i = offset; i < offset + limit; i++) {
      data[i] = { index: 1, text: `item ${i}` };
    }

    items.value = data.slice(start, end);
  }, latency());
}
</script>

<template>
  <v-scroll :count="count" :get-data="getData" :items="items" />
</template>

<style></style>
