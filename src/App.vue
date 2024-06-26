
<script setup>
import FenDang from "./components/FenDang.vue";
import {ref} from "vue";

let isShowFenDang = ref(false)
function showFenDang(){
  isShowFenDang.value = true;
}

</script>

<template>
  <div class="flex-container">
    <h2>2024年世界杯预选赛亚洲区抽签仪式</h2>
    <img src="@/assets/all.jpeg" alt="Logo" class="logo">
    <button class="start-button" @click="showFenDang">开始抽签</button>
    <FenDang v-if="isShowFenDang"/>
  </div>
</template>

<style scoped>
.flex-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center; 
}

.logo {
  width: 600px;
}

.start-button {
  /* 根据需要设置合适的宽度和高度 */
  width: 100px; 
  height: 40px; 
  background-color: #4CAF50;
  border: none;
  color: white; 
  padding: 10px 20px;
  text-align: center; 
  text-decoration: none;
  font-size: 14px; 
  margin: 4px 2px;
  cursor: pointer; 
  border-radius: 8px; 
  transition: background-color 0.3s; 
}

.start-button:hover {
  background-color: #45a049;
}
</style>