<script setup lang="ts">
import VScroll from "./components/VScroll.vue";

const latency = () => Math.random() * 500 + 300;
const count = 500;

async function getData(offset: number, limit: number) {
  return new Promise((resolve) => {
    const data: any[] = [];
    const start = Math.max(0, offset);
    const end = Math.min(offset + limit - 1, count);

    for (let i = start; i <= end; i++) {
      data.push({ index: i, text: `item ${i}` });
    }

    setTimeout(() => resolve(data), latency());
  });
}
</script>

<template><v-scroll :count="count" :get-data="getData" /></template>

<style></style>
