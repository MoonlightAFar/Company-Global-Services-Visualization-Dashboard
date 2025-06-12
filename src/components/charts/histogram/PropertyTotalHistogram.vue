<template>
  <base-card :title="name + '分类统计'">
    <div ref="deviceTotalBar" class="w-full h-full bg-[#f7faff] rounded-xl p-3" />
  </base-card>
</template>

<script lang="ts" setup>
import echarts from '@/echarts';
import { useEcharts } from '@/hooks/useEcharts';
import { STAT, siteList, siteNames } from '@/types';
import { BarChart, BarSeriesOption } from 'echarts/charts';
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
  | BarSeriesOption
  | GridComponentOption
  | TooltipComponentOption
  | LegendComponentOption
  | XAXisOption
  | YAXisOption
>;

const props = defineProps<{
  name: string;
  stat: STAT;
  dataObj: { [keys: string]: string };
}>();

const sites: string[] = [];
const deviceTotalBar = ref<HTMLElement>();

const option = reactive<ECOption>({
  backgroundColor: '#f7faff',
  color: ['#4C9AFF', '#36B37E', '#FFAB00', '#FF5630'], // 明亮而高区分度的配色
  legend: {
    data: sites,
    textStyle: {
      color: '#333',
      fontSize: 12
    },
    top: 10,
    itemGap: 10
  },
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
    top: 50,
    bottom: 40,
    left: 100,
    right: 30
  },
  xAxis: {
    type: 'value',
    axisLine: {
      lineStyle: {
        color: '#ccc'
      }
    },
    axisLabel: {
      color: '#555'
    },
    splitLine: {
      lineStyle: {
        color: '#eee'
      }
    }
  },
  yAxis: {
    type: 'category',
    data: [],
    axisTick: { show: false },
    axisLine: { show: false },
    axisLabel: {
      color: '#333',
      fontSize: 12
    }
  },
  series: []
});

echarts.use(BarChart);

const { clearChart } = useEcharts({ dom: deviceTotalBar, option: option });
const useStore = useAppStore();

watch(
  () => props.stat,
  (val) => {
    clearChart();
    sites.length = 0;

    option.series = [
      {
        name: '总量',
        type: 'bar',
        barGap: '-100%',
        barMaxWidth: 10,
        itemStyle: {
          color: props.stat !== 'statUser' ? '#dfe3e6' : '#4C9AFF',
          shadowColor: 'rgba(0, 0, 0, 0.1)',
          shadowBlur: 4,
          shadowOffsetX: 1,
          shadowOffsetY: 1
        },
        label: {
          show: true,
          position: 'right',
          color: '#666',
          fontSize: 12
        },
        data: []
      }
    ];

    option.yAxis = {
      ...option.yAxis,
      data: props.stat !== 'statUser' ? Object.values(props.dataObj) : []
    };

    useStore.setTotalCount(val).then((res) => {
      if (Array.isArray(option.series)) {
        option.series[0].data = res.yaxis;
        if (props.stat === 'statUser') {
          const newNames = Array.from(new Set(res.xaxis)).map((item) => props.dataObj[item]);
          option.yAxis = {
            ...option.yAxis,
            data: newNames
          };
        }
      }
    });

    if (props.stat !== 'statUser') {
      for (let i = 0; i < siteList.length; i++) {
        useStore.setPropertyCount(val, siteList[i]).then((res) => {
          if (Array.isArray(option.series)) {
            sites.push(siteNames[i]);
            option.series.push({
              name: siteNames[i],
              type: 'bar',
              stack: 'data',
              barMaxWidth: 25,
              data: res,
              itemStyle: {
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowBlur: 4,
                shadowOffsetX: 1,
                shadowOffsetY: 1
              },
              label: {
                show: false
              }
            });
          }
        });
      }
    }
  },
  { immediate: true }
);
</script>
