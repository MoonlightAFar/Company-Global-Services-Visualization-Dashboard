<template>
  <div class="flex flex-col items-center justify-center w-full h-screen bg-gradient-to-br from-blue-50 to-white">
    <div class="relative flex items-center justify-center w-40 h-40">
      <img
        class="absolute w-full h-full animate-spin-slow drop-shadow-md"
        src="@/assets/loading_circle_outer.png"
        alt="outer ring"
      />
      <img
        class="absolute w-4/5 h-4/5 animate-spin-slow-reverse drop-shadow-sm"
        src="@/assets/loading_circle_inner.png"
        alt="inner ring"
      />
      <img
        src="@/assets/logo.svg"
        alt="logo"
        class="w-14 h-14 z-10 drop-shadow-lg"
      />
    </div>

    <div class="mt-6 text-lg text-blue-600 font-medium">
      加载中，正在获取最新数据...
    </div>
    <div class="mt-2 text-sm text-gray-500">
      请稍等（{{ rqCount + '/' + initCount }}）
    </div>
  </div>
</template>

<script lang="ts" setup>
defineProps<{
  rqCount: number;
  initCount: number;
}>();
</script>

<style scoped>
@keyframes spin-slow {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes spin-slow-reverse {
  0% {
    transform: rotate(360deg);
  }
  100% {
    transform: rotate(0deg);
  }
}

.animate-spin-slow {
  animation: spin-slow 4s linear infinite;
}

.animate-spin-slow-reverse {
  animation: spin-slow-reverse 4s linear infinite;
}
</style>
