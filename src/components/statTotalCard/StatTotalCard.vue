<template>
  <div
    class="base-card p-4 rounded-xl cursor-pointer transition-transform duration-200 hover:scale-105"
    :style="{ borderColor: showBorder ? color : '#e5e7eb' }"
  >
    <div class="flex items-center justify-between">
      <div
        class="w-12 h-12 flex items-center justify-center rounded-full shadow-md"
        :style="{
          background: `linear-gradient(135deg, ${color}, ${lightenColor(color, 30)})`
        }"
      >
        <img class="w-6 h-6" :src="img" alt="icon" />
      </div>
      <p class="text-base font-semibold text-gray-600 xl:text-lg 2xl:text-xl">
        {{ title }}
      </p>
    </div>
    <div class="mt-3 text-right">
      <p class="text-2xl font-extrabold text-gray-800 xl:text-3xl 2xl:text-4xl">
        {{ count }}
      </p>
    </div>
  </div>
</template>

<script lang="ts" setup>
defineProps<{
  img: string;
  title: string;
  count: number;
  color: string;
  showBorder: boolean;
}>();

/**
 * 辅助函数：加亮颜色（简易 HSL 增亮）
 */
function lightenColor(color: string, percent: number) {
  try {
    const rgb = color.match(/\w\w/g)?.map((x) => parseInt(x, 16)) ?? [200, 200, 200];
    const lighter = rgb.map((v) => Math.min(255, Math.floor(v + (255 - v) * (percent / 100))));
    return `rgb(${lighter.join(',')})`;
  } catch {
    return color;
  }
}
</script>

<style scoped>
.base-card {
  background-color: #f9fafb;
  border: 2px solid #e5e7eb;
  box-shadow:
    4px 4px 8px rgba(0, 0, 0, 0.05),
    -4px -4px 8px rgba(255, 255, 255, 0.6);
  transition: box-shadow 0.3s ease;
}

.base-card:hover {
  box-shadow:
    6px 6px 14px rgba(0, 0, 0, 0.08),
    -4px -4px 10px rgba(255, 255, 255, 0.7);
}
</style>
