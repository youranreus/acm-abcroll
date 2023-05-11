<script setup lang="ts">
import { RouterView } from "vue-router";
</script>

<template>
  <header>
    <h1>🎉 SZTU ACM 社团之夜 签到大奖赛 <span>🎉</span></h1>
  </header>

  <RouterView />
</template>

<style scoped lang="scss">
header {
  margin-top: 68px;
  width: 100%;

  h1 {
    text-align: center;
    font-size: 32px;
    font-weight: 600;
    margin: 1rem 0;

    span {
      transform: rotateY(180deg);
      display: inline-block;
    }
  }
}
</style>
