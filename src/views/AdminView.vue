<template>
  <div class="admin">
    <div>
      <h3>当前cid: {{ currentCid }}</h3>

      <a-space direction="vertical">
        <a-input-group>
          <a-input-password
            v-model="pwd"
            :style="{ width: '320px' }"
            placeholder="请输入秘钥"
          >
          </a-input-password>
        </a-input-group>
        <a-input-group>
          <a-input-number
            v-model="manualCid"
            :min="42"
            :max="300"
          ></a-input-number>
          <a-button type="primary" @click="onSet" status="success"
            >设置</a-button
          >
          <a-button type="primary" @click="onRoll">随机</a-button>
        </a-input-group>
      </a-space>
    </div>
  </div>
</template>
<script setup lang="ts">
import axios from "axios";
import { random } from "lodash-es";
import { ref, onMounted, getCurrentInstance } from "vue";

const pwd = ref("");
const currentCid = ref(0);
const manualCid = ref(42);
const message =
  getCurrentInstance()?.appContext.config.globalProperties.$message;

const onRoll = () => {
  const randCid = random(42, 300);
  axios
    .post("http://81.71.47.149:20086/api/abcroll", {
      cid: randCid,
      pid: 0,
      passwd: pwd.value,
    })
    .then(() => {
      currentCid.value = randCid;
      manualCid.value = randCid;
    })
    .catch((error) => {
      message?.error(error.message);
    });
};

const onSet = () => {
  axios
    .post("http://81.71.47.149:20086/api/abcroll", {
      cid: manualCid.value,
      pid: 0,
      passwd: pwd.value,
    })
    .then(() => {
      currentCid.value = manualCid.value;
    })
    .catch((error) => {
      message?.error(error.message);
    });
};

onMounted(() => {
  axios
    .get("http://81.71.47.149:20086/api/abcroll")
    .then((res) => {
      currentCid.value = res.data.data.cid;
      manualCid.value = res.data.data.cid;
    })
    .catch((error) => {
      message?.error(error.message);
    });
});
</script>
<style lang="scss" scoped>
.admin {
  width: 90%;
  margin: 0 auto;
  border-radius: 16px;
  box-shadow: 0 6px 12px 0 rgb(31 35 41 / 8%);

  min-height: 500px;
  box-sizing: border-box;
  overflow: hidden;

  background-color: rgb(250, 250, 250);

  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
