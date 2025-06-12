<template>
  <base-card :title="name + '保有量'">
    <div ref="historyTotalBar" class="w-full h-full bg-[#f7faff] rounded-xl p-3" />
    <data-zoom-component />
  </base-card>
</template>

<script lang="ts" setup>
import echarts from '@/echarts';
import { useEcharts } from '@/hooks/useEcharts';
import { STAT } from '@/types';
import { xMonths } from '@/utils/time';
import { BarChart, BarSeriesOption } from 'echarts/charts';
import {
  TooltipComponentOption,
  DataZoomComponentOption,
  DataZoomComponent,
  GridComponentOption
} from 'echarts/components';
import { XAXisOption, YAXisOption } from 'echarts/types/dist/shared';
import { reactive, ref, watch } from 'vue';
import BaseCard from '@/components/baseCard/BaseCard.vue';
import { useAppStore } from '@/store';

type ECOption = echarts.ComposeOption<
  | BarSeriesOption
  | GridComponentOption
  | TooltipComponentOption
  | DataZoomComponentOption
  | XAXisOption
  | YAXisOption
>;

const props = defineProps<{
  name: string;
  stat: STAT;
}>();

const historyTotalBar = ref<HTMLElement>();
const option = reactive<ECOption>({
  backgroundColor: '#f7faff',
  tooltip: {
    trigger: 'axis',
    backgroundColor: '#fff',
    borderColor: '#ccc',
    borderWidth: 1,
    textStyle: {
      color: '#333',
      fontSize: 12
    },
    axisPointer: {
      type: 'shadow',
      shadowStyle: {
        color: 'rgba(0,0,0,0.05)'
      }
    }
  },
  grid: {
    top: 30,
    bottom: 60,
    left: 60,
    right: 20
  },
  xAxis: {
    data: xMonths,
    axisTick: { show: false },
    axisLine: { show: false },
    axisLabel: {
      color: '#555',
      fontSize: 11,
      rotate: 35
    }
  },
  yAxis: {
    min: (value) => (2 * value.min - value.max < 0 ? 0 : 2 * value.min - value.max),
    axisLine: { show: false },
    splitLine: {
      lineStyle: {
        color: '#eee'
      }
    },
    axisLabel: {
      color: '#555',
      fontSize: 12
    }
  },
  dataZoom: [
    {
      type: 'slider',
      show: true,
      bottom: 15,
      height: 10,
      startValue: xMonths.length - 10,
      endValue: xMonths.length - 1,
      backgroundColor: '#fff',
      borderColor: '#ccc',
      handleStyle: {
        color: '#4C9AFF'
      },
      textStyle: {
        color: '#999'
      }
    },
    {
      type: 'inside',
      start: 1,
      end: 35
    }
  ],
  series: []
});

echarts.use(BarChart);
useEcharts({ dom: historyTotalBar, option });

const useStore = useAppStore();

watch(
  () => props.stat,
  (val) => {
    useStore
      .setHistoryCount(val)
      .then((res) => {
        option.series = {
          name: props.name,
          type: 'bar',
          barMaxWidth: 30,
          data: res,
          itemStyle: {
            color: '#36B37E', // 柔和绿色，统一风格
            borderRadius: [4, 4, 0, 0],
            shadowBlur: 4,
            shadowColor: 'rgba(0, 0, 0, .1)',
            shadowOffsetX: 2,
            shadowOffsetY: 2
          },
          label: {
            show: true,
            position: 'top',
            fontSize: 11,
            color: '#333'
          }
        };
      })
      .catch(() => null);
  },
  { immediate: true }
);
</script>
