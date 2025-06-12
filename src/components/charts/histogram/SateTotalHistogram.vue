<template>
  <base-card :title="'四大核心站点' + name + '量'">
    <div ref="stateTotalPie" class="w-full h-full" />
  </base-card>
</template>

<script lang="ts" setup>
import echarts from '@/echarts';
import { useEcharts } from '@/hooks/useEcharts';
import { STAT, siteNames } from '@/types';
import { PieChart, PieSeriesOption } from 'echarts/charts';
import { TooltipComponentOption, LegendComponentOption } from 'echarts/components';
import { reactive, ref, watch } from 'vue';
import BaseCard from '@/components/baseCard/BaseCard.vue';
import { useAppStore } from '@/store';

type ECOption = echarts.ComposeOption<
  PieSeriesOption | TooltipComponentOption | LegendComponentOption
>;

const props = defineProps<{
  name: string;
  stat: STAT;
}>();

const stateTotalPie = ref<HTMLElement | null>(null);

const myColor = ['#4e79a7', '#76b7b2', '#59a14f', '#edc948', '#f28e2b'];

const option = reactive<ECOption>({
  tooltip: {
    trigger: 'item',
    formatter: '{b}: {c} ({d}%)'
  },
  legend: {
    orient: 'vertical',
    left: 10,
    top: 'center',
    textStyle: {
      color: '#555',
      fontSize: 12
    }
  },
  series: [
    {
      name: '站点分布',
      type: 'pie',
      radius: ['40%', '70%'],
      center: ['60%', '50%'],
      avoidLabelOverlap: false,
      label: {
        show: true,
        position: 'outside',
        formatter: '{b}: {d}%',
        fontSize: 12
      },
      labelLine: {
        length: 10,
        length2: 10
      },
      itemStyle: {
        borderRadius: 6,
        borderColor: '#fff',
        borderWidth: 2
      },
      data: []
    }
  ]
});

echarts.use(PieChart);
useEcharts({ dom: stateTotalPie, option });

const useStore = useAppStore();

watch(
  () => props.stat,
  (val) => {
    useStore
      .setSitesCount(val)
      .then((res) => {
        option.series[0].data = siteNames.map((name, i) => ({
          name,
          value: res[i],
          itemStyle: {
            color: myColor[i % myColor.length]
          }
        }));
      })
      .catch(() => null);
  },
  {
    immediate: true
  }
);
</script>
