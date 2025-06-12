<template>
  <base-card :title="name + '种类占比'">
    <div ref="propertyPie" class="w-full h-full bg-[#f9fbfd] rounded-lg" />
  </base-card>
</template>

<script lang="ts" setup>
import echarts from '@/echarts';
import { useEcharts } from '@/hooks/useEcharts';
import { STAT } from '@/types';
import { PieChart, PieSeriesOption } from 'echarts/charts';
import {
  PolarComponent,
  PolarComponentOption,
  TitleComponentOption,
  TooltipComponentOption
} from 'echarts/components';
import { AngleAxisOption, RadiusAxisOption } from 'echarts/types/dist/shared';
import { reactive, ref, watch } from 'vue';
import BaseCard from '@/components/baseCard/BaseCard.vue';
import { useAppStore } from '@/store';

type ECOption = echarts.ComposeOption<
  | PieSeriesOption
  | TitleComponentOption
  | TooltipComponentOption
  | PolarComponentOption
  | AngleAxisOption
  | RadiusAxisOption
>;

const props = defineProps<{
  name: string;
  stat: STAT;
  dataObj: { [keys: string]: string };
}>();

const propertyPie = ref<HTMLElement>();

const option = reactive<ECOption>({
  backgroundColor: '#f9fbfd', // ✅ 更浅的背景色
  tooltip: {
    trigger: 'item',
    backgroundColor: '#ffffff',
    borderColor: '#dfe6ec',
    borderWidth: 1,
    textStyle: {
      color: '#333',
      fontSize: 13
    },
    formatter: '{b} : {c} ({d}%)'
  },
  polar: {},
  angleAxis: {
    interval: 1,
    type: 'category',
    data: [],
    z: 10,
    axisLine: {
      show: false
    },
    axisLabel: {
      interval: 0,
      show: true,
      color: '#4f5b62',
      margin: 8,
      fontSize: 14
    }
  },
  radiusAxis: {
    min: 0,
    max: 10,
    interval: 25,
    axisLine: {
      show: false
    },
    axisLabel: {
      show: false
    },
    axisTick: {
      show: false
    },
    splitLine: {
      show: false
    }
  },
  series: {
    type: 'pie',
    radius: ['0%', '80%'], // ✅ 尺寸不变
    zlevel: 10,
    startAngle: 200,
    itemStyle: {
      borderRadius: 12,
      borderColor: '#fff',
      borderWidth: 2,
      shadowBlur: 6,
      shadowColor: 'rgba(0, 0, 0, 0.08)',
      shadowOffsetX: 2,
      shadowOffsetY: 2
    },
    label: {
      fontSize: 13,
      color: '#666',
      formatter: ['{b|{b}}', '{d|{d}%}'].join('\n'),
      rich: {
        b: {
          color: '#666',
          lineHeight: 20
        },
        d: {
          color: '#007BFF',
          fontWeight: 'bold',
          fontSize: 14,
          height: 20
        }
      }
    },
    labelLine: {
      show: true,
      length: 10,
      length2: 12,
      smooth: true,
      lineStyle: {
        width: 1.5,
        color: '#ccc'
      }
    },
    data: []
  }
});

echarts.use([PieChart, PolarComponent]);

useEcharts({ dom: propertyPie, option: option });

const useStore = useAppStore();

watch(
  () => props.stat,
  (val) => {
    useStore
      .setTotalCount(val)
      .then((res) => {
        if (!Array.isArray(option.series)) {
          option.series?.data?.splice(0, option.series.data.length);
          const { nameList, dataList } =
            val === 'statUser'
              ? filterUser(res.xaxis, res.yaxis)
              : { nameList: res.xaxis, dataList: res.yaxis };
          for (let i = 0; i < dataList.length; i++) {
            option.series?.data?.push({
              value: dataList[i],
              name: props.dataObj[nameList[i]],
              itemStyle: {
                shadowBlur: 6,
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowOffsetX: 2,
                shadowOffsetY: 2
              }
            });
          }
        }
      })
      .catch(() => null);
  },
  {
    immediate: true
  }
);

/**
 * 将用户量统计中少于1000筛选为其他
 */
function filterUser(xList: string[], yList: number[]) {
  let otherSum = 0;
  const nameList = [];
  const dataList = yList.filter((item, index) => {
    if (item < 1000) {
      otherSum += item;
      return false;
    }
    nameList.push(xList[index]);
    return true;
  });
  nameList.push('其他');
  dataList.push(otherSum);
  return { nameList, dataList };
}
</script>

<style scoped>
/* 可选美化容器 */
</style>
