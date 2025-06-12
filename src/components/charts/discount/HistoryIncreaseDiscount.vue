<template>
  <base-card :title="name + '历史增量'">
    <div ref="historyIncreaseDiscount" class="w-full h-full bg-[#f0f8ff] rounded-xl p-2" />
  </base-card>
</template>

<script lang="ts" setup>
import echarts from '@/echarts';
import { useEcharts } from '@/hooks/useEcharts';
import { STAT, siteList, siteNames } from '@/types';
import { xMonths } from '@/utils/time';
import { LineChart, LineSeriesOption } from 'echarts/charts';
import {
  TooltipComponentOption,
  LegendComponentOption,
  GridComponentOption
} from 'echarts/components';
import { XAXisOption, YAXisOption } from 'echarts/types/dist/shared';
import { reactive, ref, watch } from 'vue';
import BaseCard from '@/components/baseCard/BaseCard.vue';
import { useAppStore } from '@/store';

type ECOption = echarts.ComposeOption<
  | LineSeriesOption
  | GridComponentOption
  | TooltipComponentOption
  | LegendComponentOption
  | XAXisOption
  | YAXisOption
>;

const props = defineProps<{
  name: string;
  stat: STAT;
}>();
const sites: string[] = [];
const historyIncreaseDiscount = ref<HTMLElement>();
const option = reactive<ECOption>({
  backgroundColor: '#f0f8ff', // 淡蓝背景
  legend: {
    data: sites,
    itemWidth: 10,
    textStyle: {
      color: '#333',
      fontSize: 12
    },
    top: 10
  },
  tooltip: {
    trigger: 'axis',
    backgroundColor: 'rgba(255, 255, 255, 0.95)',
    borderColor: '#ccc',
    borderWidth: 1,
    textStyle: {
      color: '#333',
      fontSize: 12
    },
    axisPointer: {
      type: 'shadow',
      shadowStyle: {
        color: 'rgba(0, 0, 0, 0.05)'
      }
    }
  },
  grid: {
    top: 60,
    bottom: 60,
    left: 60,
    right: 20
  },
  xAxis: {
    type: 'category',
    data: xMonths.slice(1),
    axisTick: {
      show: false
    },
    axisLine: {
      lineStyle: {
        color: '#ccc'
      }
    },
    axisLabel: {
      color: '#555'
    }
  },
  yAxis: {
    axisLabel: {
      color: '#555'
    },
    splitLine: {
      lineStyle: {
        color: '#ddd'
      }
    }
  },
  dataZoom: [
    {
      type: 'slider',
      show: true,
      bottom: 15,
      height: 10,
      startValue: xMonths.length - 11,
      endValue: xMonths.length - 2,
      moveHandleSize: 15,
      textStyle: {
        color: '#666'
      },
      backgroundColor: '#e6f2ff',
      fillerColor: '#aed6f1',
      borderColor: '#cce5ff',
      handleStyle: {
        color: '#5dade2',
        borderColor: '#fff'
      }
    },
    {
      type: 'inside',
      show: true,
      height: 15,
      start: 1,
      end: 35
    }
  ],
  series: []
});

echarts.use(LineChart);
useEcharts({ dom: historyIncreaseDiscount, option: option });

const useStore = useAppStore();

watch(
  () => props.stat,
  () => {
    getData();
  },
  {
    immediate: true
  }
);

/**
 * 获取数据
 */
async function getData() {
  sites.length = 0;
  option.series = [
    {
      name: '全球',
      type: 'line',
      data: [],
      emphasis: {
        focus: 'series'
      },
      symbol: 'circle',
      smooth: true,
      lineStyle: {
        color: '#3498db',
        width: 2
      },
      itemStyle: {
        color: '#3498db'
      }
    }
  ];
  sites.push('全球');

  useStore
    .setHistoryIncrement(props.stat)
    .then((res) => {
      if (Array.isArray(option.series)) {
        option.series[0].data = res;
      }
    })
    .catch(() => null);

  for (let i = 0; i < siteList.length; i++) {
    useStore
      .setPropertyHistoryIncrement(props.stat, siteList[i])
      .then((res) => {
        if (Array.isArray(option.series)) {
          sites.push(siteNames[i]);
          option.series.push({
            name: siteNames[i],
            type: 'line',
            data: res,
            emphasis: {
              focus: 'series'
            },
            smooth: true,
            lineStyle: {
              type: 'dashed',
              opacity: 0.5
            },
            itemStyle: {
              opacity: 0.5
            }
          });
        }
      })
      .catch(() => null);
  }
}
</script>
