<script setup lang="ts">
import { ref, onMounted } from "vue";
const props = defineProps<{
  count: number;
  getData: Function;
}>();

const { count } = props;
const itemHeight = 20;
const obs = ref();
const data = ref([]);
const dataLoading = ref(true);

const totalHeight = count * itemHeight;
const obsHeight = 50 * itemHeight;

onMounted( async () => {
  data.value = await props.getData(0, 50);
  dataLoading.value = false;
})
</script>

<template>
  <div :class="$style.viewport">
    <div :style="{ height: `${totalHeight}px` }">
      <div ref="obs" :style="{ height: `${obsHeight}px` }" :class="$style.obs">
        <div v-if="dataLoading">loading...</div>
        <div v-else>
          <div v-for="{index, text} in data" :key="index">{{ text }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" module>
.viewport {
  overflow: auto;
  height: 200px;

  resize: vertical;
}

.obs {
  background-color: lime;
}
</style>
