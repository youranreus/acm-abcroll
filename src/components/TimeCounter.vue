<template>
  <div class="counter">
    <span>耗时</span>
    <h3 v-if="!loading">{{ displayTime }}</h3>
    <h3 v-else>加载中...</h3>
  </div>
</template>
<script lang="ts" setup>
import { ref, watch, computed } from "vue";
import { padStart } from "lodash-es";

const props = defineProps({
  running: {
    type: Boolean,
    default: false,
  },
  startFrom: {
    type: Number,
    default: () => new Date().getTime(),
  },
  loading: {
    type: Boolean,
    default: false,
  },
});

const currentTime = ref(new Date().getTime());
const timer = ref(0);

const displayTime = computed(() => {
  const trueVal = currentTime.value - props.startFrom;
  const val = trueVal < 10 ? 0 : trueVal;

  const minutes = Math.floor(val / 60000);
  const seconds = Math.floor((val - minutes * 60000) / 1000);
  const millis = Math.floor(val - minutes * 60000 - seconds * 1000);

  return `${padStart(`${minutes}`, 2, "0")}:${padStart(
    `${seconds}`,
    2,
    "0"
  )}:${padStart(`${millis}`, 3, "0")}`;
});

watch(
  () => props.startFrom,
  (val) => {
    if (val) {
      currentTime.value = val;
    }
  }
);

watch(
  () => props.running,
  (val) => {
    if (val) {
      timer.value = setInterval(() => {
        currentTime.value = new Date().getTime();
      }, 1);
    } else {
      clearInterval(timer.value);
    }
  }
);
</script>
<style lang="scss" scoped>
.counter {
  display: flex;
  width: 200px;
  flex-direction: column;
  justify-content: space-between;
  box-sizing: border-box;
  height: 100%;
  text-align: center;

  span {
    color: var(--color-text-2);
    font-size: 14px;
  }

  h3 {
    margin: 0;
    font-size: 36px;
    font-weight: 600;
    width: 150px;
  }
}
</style>
