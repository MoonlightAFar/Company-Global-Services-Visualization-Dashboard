<template>
  <div class="flex flex-col w-full h-screen p-2 bg-gray-100">
    <!-- 大标题栏，含动画 Logo 和居中文字 -->
    <div
      class="flex items-center justify-center bg-blue-600 text-white py-4 px-6 rounded relative"
      style="font-family: 'Orbitron', sans-serif;"
    >
      <!-- 动画 Logo 区域，绝对定位左侧 -->
      <div class="absolute left-6 w-12 h-12">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 64 64"
          class="w-12 h-12 animate-spin-slow"
        >
          <defs>
            <linearGradient id="grad1" x1="0%" y1="0%" x2="100%" y2="100%">
              <stop offset="0%" stop-color="#00f0ff" />
              <stop offset="100%" stop-color="#0077ff" />
            </linearGradient>
            <filter id="glow" x="-50%" y="-50%" width="200%" height="200%">
              <feDropShadow
                dx="0"
                dy="0"
                stdDeviation="3"
                flood-color="#00bfff"
                flood-opacity="0.7"
              />
              <feDropShadow
                dx="0"
                dy="0"
                stdDeviation="6"
                flood-color="#0077ff"
                flood-opacity="0.5"
              />
            </filter>
          </defs>
          <!-- 外圈六边形，带渐变填充和发光 -->
          <polygon
            points="32 4 12 16 12 48 32 60 52 48 52 16 32 4"
            fill="url(#grad1)"
            filter="url(#glow)"
            stroke="#00bfff"
            stroke-width="1.5"
          />
          <!-- 内部三角形 -->
          <path
            d="M32 18 L20 40 L44 40 Z"
            fill="url(#grad1)"
            stroke="#00bfff"
            stroke-width="1.5"
            filter="url(#glow)"
          />
          <!-- 中心圆 -->
          <circle
            cx="32"
            cy="38"
            r="4"
            fill="#00e5ff"
            filter="url(#glow)"
            stroke="#00bfff"
            stroke-width="1"
          />
        </svg>
      </div>

      <!-- 居中标题文本 -->
      <h1
        class="text-3xl font-bold select-none drop-shadow-md"
        style="text-shadow: 0 0 8px #00bfff;"
      >
        CrowdStrike公司全球服务可视化大屏
      </h1>
    </div>

    <!-- 主内容区域 -->
    <div class="flex flex-1 mt-4">
      <div class="w-1/4">
        <history-total-histogram :name="name" :stat="stat" class="h-1/3" />
        <history-increase-discount :name="name" :stat="stat" class="h-1/3" />
        <sate-total-histogram :name="name" :stat="stat" class="h-1/3" />
      </div>
      <div class="flex flex-col w-1/2">
        <stat-menu @click="handleClick" />
        <world-user-map class="flex-1" />
        <div class="flex h-1/4">
          <proportion-total-histogram
            title="服务购买率"
            out-name="客户量"
            out-stat="statUser"
            in-name="订阅客户量"
            in-stat="statCloudStorageUser"
            :is-pre="true"
            class="w-1/2 h-full"
          />
          <proportion-total-histogram
            title="人均终端数"
            out-name="客户量"
            out-stat="statUser"
            in-name="终端量"
            in-stat="statDevice"
            class="w-1/2 h-full"
          />
        </div>
      </div>
      <div class="w-1/4">
        <property-pie :name="name" :stat="stat" :data-obj="dataObj" class="h-1/3" />
        <property-total-histogram :name="name" :stat="stat" :data-obj="dataObj" class="h-2/3" />
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { deviceObj, cloudServiceObj, countryObj } from '@/types/response';
import HistoryTotalHistogram from '@/components/charts/histogram/HistoryTotalHistogram.vue';
import SateTotalHistogram from '@/components/charts/histogram/SateTotalHistogram.vue';
import PropertyTotalHistogram from '@/components/charts/histogram/PropertyTotalHistogram.vue';
import HistoryIncreaseDiscount from '@/components/charts/discount/HistoryIncreaseDiscount.vue';
import ProportionTotalHistogram from '@/components/charts/histogram/ProportionTotalHistogram.vue';
import PropertyPie from '@/components/charts/pie/PropertyPie.vue';
import { STAT } from '@/types';
import StatMenu from '../components/menu/StatMenu.vue';
import { Ref, ref } from 'vue';
import WorldUserMap from '@/components/charts/map/WorldUserMap.vue';

const stat: Ref<STAT> = ref('statUser');
const dataObj: Ref<{ [keys: string]: string }> = ref(countryObj);
const name = ref('客户');

function handleClick({ dataId }: { dataId: STAT; color: string }) {
  stat.value = dataId;
  switch (dataId) {
    case 'statUser': {
      dataObj.value = countryObj;
      name.value = '客户';
      break;
    }
    case 'statDevice': {
      dataObj.value = deviceObj;
      name.value = '终端';
      break;
    }
    case 'statCloudStorageService': {
      dataObj.value = cloudServiceObj;
      name.value = '套餐订阅';
      break;
    }
    case 'statCloudStorageAmount': {
      dataObj.value = cloudServiceObj;
      name.value = '套餐盈利';
      break;
    }
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@700&display=swap');

@keyframes spin-slow {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
.animate-spin-slow {
  animation: spin-slow 10s linear infinite;
}
</style>
