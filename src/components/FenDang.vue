<template>
  <div class="group-container">
    <div v-for="(groupImages, groupName) in dynamicGroups" :key="groupName" class="group-item">
      <h2>{{ groupName }}</h2>
      <div class="image-grid">
        <div v-for="(image, index) in groupImages" :key="index" class="image-item">
          <img :src="image" :alt="`Image ${index + 1}`">
        </div>
      </div>
    </div>
    <!-- 添加闪烁图片区域 -->
    <div class="flashing-area">
      <h5 :class="tips==='抽签中...'?'tips-process-color':'tips-done-color'">{{tips}}</h5>
      <transition name="fade">
        <img v-if="flashImages.length > 0" :src="flashImages[0]" alt="Flashing Image" />
      </transition>
    </div>
  </div>
</template>

<script setup>
import {ref, onMounted, nextTick, onUnmounted} from 'vue';

const images = ref({
  level1: ['/level1/image1.svg', '/level1/image2.svg', '/level1/image3.svg'],
  level2: ['/level2/image1.svg', '/level2/image2.svg', '/level2/image3.png'],
  level3: ['/level3/image1.svg', '/level3/image2.png', '/level3/image3.png'],
  level4: ['/level4/image1.svg', '/level4/image2.svg', '/level4/image3.svg'],
  level5: ['/level5/image1.png', '/level5/image2.svg', '/level5/image3.svg'],
  level6: ['/level6/image1.png', '/level6/image2.svg', '/level6/image3.svg']
});

const dynamicGroups = ref({
  'A组': [], 'B组': [],   'C组': []
});

const currentLevel = ref('level1');
const currentGroupIndex = ref(0);
const flashImages = ref([]);
const selectedImages = ref([]);
let flashTimer = null;
let tips = ref("抽签中...");

const selectAndFlashImage = () => {
  const levelImagesCopy = [...images.value[currentLevel.value]];
  let flashIndex = 0;
  let flashSpeed = 50; // 初始闪烁速度，单位：毫秒
  let randomIndex = Math.floor(Math.random() * levelImagesCopy.length);
  let startTime = Date.now(); // 记录开始时间


  const adjustFlashSpeed = () => {
    if (Date.now() - startTime < 1800) {
      // 在前1.8秒内，根据时间线性增加闪烁速度
      flashSpeed += 5;
    } else {
      // 在最后0.2秒内，指数级增加闪烁速度
      flashSpeed = Math.min(flashSpeed * 1.5, 200);
    }
  };

  const flashTimer = setInterval(() => {
    adjustFlashSpeed();
    if (Date.now() - startTime < 1000) {
      flashImages.value = [levelImagesCopy[flashIndex]];
      flashIndex = (flashIndex + 1) % levelImagesCopy.length;
    } else {
      clearInterval(flashTimer);
      const finalImage = levelImagesCopy[randomIndex];
      flashImages.value = [finalImage]; // 设置为最终图片

      // 增加延时，让图片停留更长时间
      setTimeout(() => {
        dynamicGroups.value[Object.keys(dynamicGroups.value)[currentGroupIndex.value]].push(finalImage);
        selectedImages.value.push(finalImage);
        images.value[currentLevel.value] = images.value[currentLevel.value].filter((_, i) => i !== randomIndex);

        if (selectedImages.value.length < 18) {
          nextLevel();
        }else{
          tips.value="抽签结束，祝中国队好运";
        }
      }, 1000); // 延时2000毫秒
    }
  }, flashSpeed);
};

const nextLevel = () => {
  if (selectedImages.value.length % 6 === 0 && selectedImages.value.length < 18) {
    currentGroupIndex.value = (currentGroupIndex.value + 1) % Object.keys(dynamicGroups.value).length;
  }

  const levels = Object.keys(images.value);
  const currentIndex = levels.indexOf(currentLevel.value);
  currentLevel.value = levels[(currentIndex + 1) % levels.length];

  if (selectedImages.value.length < 18) {
    selectAndFlashImage();
  }
};

onMounted(() => {
  selectAndFlashImage();
});

</script>