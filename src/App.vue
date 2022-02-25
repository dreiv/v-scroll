<script setup lang="ts">
import { ref } from "vue";
import VScroll from "./components/VScroll.vue";

const options = ref({
  count: 16,
  itemHeight: 20,
  amount: 5,
  tolerance: 2,
});

function getData(offset: number, limit: number) {
  const data = [];
  const start = Math.max(0, offset);
  const end = Math.min(offset + limit - 1, options.value.count);

  for (let i = start; i <= end; i++) {
    data.push({ index: i, text: `item ${i}` });
  }

  return data;
}
</script>

<template><v-scroll :options="options" :get-data="getData" /></template>

<style></style>
