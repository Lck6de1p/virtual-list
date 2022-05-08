<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import VirtualList from "./components/VirtualList.vue";
import { ref } from "vue";
const arr = new Array(1000).fill().map((v, index) => {
  return {
    id: index,
  };
});
const list = ref(arr);
</script>

<template>
  <virtual-list :list-data="list">
    <template #default="{ listItem }">
      <div style="height: 50px">{{ listItem.id }}</div>
    </template>
  </virtual-list>
</template>

<style>
</style>
