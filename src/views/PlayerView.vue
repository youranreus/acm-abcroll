<template>
  <div>
    <div
      class="container"
      :class="{ red: side === 'red', blue: side === 'blue' }"
    >
      <div class="content">
        <div class="header">
          <div
            class="side"
            :class="{ red: side === 'red', blue: side === 'blue' }"
          >
            {{ side === "red" ? "红方" : "蓝方" }}
          </div>
          <div class="time">
            <time-counter
              :running="gameStarted && iframeLoaded"
              :start-from="startTime"
              :loading="gameStarted && !iframeLoaded"
            ></time-counter>
          </div>
          <div
            class="win-time"
            @click.left="winTime++"
            @click.middle="winTime = 0"
            @contextmenu.prevent="winTime > 0 && winTime--"
          >
            <span>当前已获胜</span>
            <h3>{{ winTime }} 局</h3>
          </div>
        </div>
        <div class="task">
          <template v-if="currentCid !== 0">
            <p class="question-info">
              当前题目id：<a :href="iframeUrl" target="_blank">{{
                currentCid
              }}</a>
              👈🏻 点击交题
            </p>
            <div
              class="iframe-container"
              :class="{ hidden: gameStarted && !iframeLoaded }"
            >
              <iframe
                ref="iframeRef"
                :src="iframeUrl"
                frameborder="0"
                @load="handleIframeLoaded"
              ></iframe>
            </div>
          </template>
          <div v-else class="placeholder">我的回合，抽题！👇🏻</div>
        </div>
      </div>
    </div>

    <div class="operate">
      <div
        class="content"
        :class="{ red: side === 'red', blue: side === 'blue' }"
      >
        <div :class="{ 'shake-slow shake-constant': gameStarted }">
          <submit-button
            @click="handleClickBtn"
            :class="{ red: side === 'red', blue: side === 'blue' }"
            >{{ btnText }}</submit-button
          >
        </div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref, computed, getCurrentInstance, onMounted } from "vue";
import { useRoute } from "vue-router";
import axios from "axios";
import SubmitButton from "@/components/SubmitButton.vue";
import TimeCounter from "@/components/TimeCounter.vue";

const route = useRoute();

const side = ref<string>(route.params.side as string);
const gameStarted = ref(false);
const iframeLoaded = ref(false);
const currentCid = ref(0);
const winTime = ref(0);
const startTime = ref(new Date().getTime());
const message =
  getCurrentInstance()?.appContext.config.globalProperties.$message;
const localStart = ref(false);
const iframeRef = ref<HTMLIFrameElement>();

const btnText = computed(() => {
  return gameStarted.value ? "完成" : "获取题目";
});

const iframeUrl = computed(() => {
  const num =
    currentCid.value < 100 ? `0${currentCid.value}` : currentCid.value;
  return `https://atcoder.jp/contests/abc${num}/tasks/abc${num}_a`;
});

const handleStartGame = () => {
  axios
    .get("http://81.71.47.149:20086/api/abcroll")
    .then((res) => {
      if (currentCid.value === res.data.data.cid) {
        iframeLoaded.value = true;
      }
      currentCid.value = res.data.data.cid;

      gameStarted.value = true;
      startTime.value = new Date().getTime();

      const localData = {
        started: true,
        cid: currentCid.value,
        startTime: startTime.value,
      };

      localStorage.setItem(`${side.value}_data`, JSON.stringify(localData));
    })
    .catch((error) => {
      message?.error(error.message);
    });
};

const handleStopGame = () => {
  gameStarted.value = false;
  iframeLoaded.value = false;
  const localData = {
    started: false,
    cid: currentCid.value,
    startTime: startTime.value,
  };
  localStart.value = false;

  localStorage.setItem(`${side.value}_data`, JSON.stringify(localData));
};

const handleClickBtn = () => {
  if (gameStarted.value) {
    handleStopGame();
  } else {
    handleStartGame();
  }
};

const handleIframeLoaded = () => {
  iframeLoaded.value = true;
  if (!localStart.value) startTime.value = new Date().getTime();
};

onMounted(() => {
  const localData = JSON.parse(
    localStorage.getItem(`${side.value}_data`) || '{"started":false}'
  );
  if (localData && localData.started) {
    currentCid.value = localData.cid;
    startTime.value = localData.startTime;
    gameStarted.value = true;
    localStart.value = true;
  }
});
</script>
<style lang="scss" scoped>
$redTeamColor: rgb(var(--red-4));
$blueTeamColor: rgb(var(--arcoblue-4));

.container {
  width: 90%;
  margin: 0 auto;
  border-radius: 16px;
  box-shadow: 0 6px 12px 0 rgb(31 35 41 / 8%);

  min-height: 500px;
  box-sizing: border-box;
  overflow: hidden;

  &.red {
    background-color: $redTeamColor;
  }

  &.blue {
    background-color: $blueTeamColor;
  }

  .content {
    margin: 16px;
    width: calc(100% - 32px);
    height: calc(100vh - 216px - 3.5rem);
    background: RGBA(255, 255, 255, 0.95);
    border-radius: 16px;
  }

  .header {
    height: 96px;
    width: 100%;
    display: flex;
    justify-content: space-between;

    .side {
      height: 96px;
      width: 156px;
      line-height: 96px;
      text-align: center;

      font-size: 2rem;
      color: #fff;
      border-bottom-right-radius: 16px;

      &.blue {
        background: $blueTeamColor;
      }

      &.red {
        background-color: $redTeamColor;
      }
    }

    .win-time {
      box-sizing: border-box;
      height: 96px;
      padding: 10px;
      display: flex;
      justify-content: space-between;
      flex-direction: column;
      user-select: none;
      cursor: pointer;

      span {
        color: var(--color-text-2);
        font-size: 14px;
      }

      h3 {
        margin: 0;
        font-size: 36px;
      }
    }

    .time {
      box-sizing: border-box;
      height: 96px;
      padding: 10px;

      :deep(.arco-statistic-value) {
        font-size: 36px;
        font-weight: 600;
      }
    }
  }

  .task {
    width: 100%;
    height: calc(100vh - 312px - 3.5rem);
    box-sizing: border-box;
    padding: 16px;

    .placeholder {
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;

      font-size: 32px;
      font-style: italic;
      opacity: 0.4;
      font-weight: 400;
    }

    p.question-info {
      font-size: 20px;
      margin: 0 0 5px;
      opacity: 0.7;
      font-weight: 600;
    }

    .iframe-container {
      width: 100%;
      height: calc(100% - 25px);

      &.hidden {
        opacity: 0;
      }

      iframe {
        width: 100%;
        height: 100%;
      }
    }
  }
}

.operate {
  position: fixed;
  bottom: 20px;
  left: 0;
  width: 100vw;

  text-align: center;
}
</style>
