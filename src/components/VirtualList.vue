<script setup>
import {
  ref,
  computed,
  nextTick,
  reactive,
  watchEffect,
  onUnmounted,
} from "vue";
import { useEventListener } from "../composables/event";

const props = defineProps({
  listData: Array,
});

const ulRef = ref(null);

const screenH = document.documentElement.clientHeight;

const data = reactive({
  // 列表第一项的高度（起始高度）
  initH: 0,

  // 一行的高度
  unitH: 0,

  // 屏幕范围内能显示个数
  displayCount: 1,

  // 列表起始值
  startIdx: 0,
});

function scrollHandler() {
  // 当前滚动高度
  const curScrollTop = document.documentElement.scrollTop;
  if (curScrollTop > data.initH) {
    const addCount = Math.floor((curScrollTop - data.initH) / data.unitH);
    ulRef.value.style.setProperty("padding-top", `${addCount * data.unitH}px`);
    data.startIdx = addCount;
  } else {
    ulRef.value.style.setProperty("padding-top", "0px");
    data.startIdx = 0;
  }
}

const listData = computed(() => {
  return props.listData
    .slice(data.startIdx, data.startIdx + data.displayCount)
    .map((v, index) => {
      return { ...v, idx: (data.startIdx + index) % data.displayCount };
    });
});

watchEffect(() => {
  if (props.listData.length > 0) {
    nextTick(() => {
      // 列表距离顶部距离
      data.initH =
        ulRef.value.getBoundingClientRect().top +
        document.documentElement.scrollTop;
      // 计算每行高度
      data.unitH = ulRef.value.children[0].offsetHeight;
      // 计算屏幕内能显示的行数
      data.displayCount = Math.ceil(screenH / data.unitH);
      // 设置列表总高度 = 一行高度 * 行数
      const listH = data.unitH * props.listData.length;
      ulRef.value.style.setProperty("height", `${listH}px`);
    });
  }
});

useEventListener(window, "scroll", scrollHandler);
</script>
<template>
  <ul ref="ulRef">
    <li
      v-for="listItem in listData"
      :key="`list-${listItem.idx}`"
      :data-idx="listItem.idx"
    >
      <slot :listItem="listItem"></slot>
    </li>
  </ul>
</template>
