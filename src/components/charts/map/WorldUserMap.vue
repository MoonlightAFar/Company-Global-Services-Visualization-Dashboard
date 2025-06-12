<template>
  <base-card title="全球服务网络覆盖图" class="world-map-card">
    <div ref="worldUserMap" class="world-map-container" />
  </base-card>
</template>

<script lang="ts" setup>
import BaseCard from '@/components/baseCard/BaseCard.vue';
import echarts from '@/echarts';
import { useEcharts } from '@/hooks/useEcharts';
import {
  EffectScatterChart,
  EffectScatterSeriesOption,
  LinesChart,
  LinesSeriesOption,
  MapChart,
  MapSeriesOption
} from 'echarts/charts';
import { GeoComponentOption, TooltipComponentOption } from 'echarts/components';
import { ref, reactive } from 'vue';
import map from '@/static/map.json';
import { useAppStore } from '@/store';
import { countryObj } from '@/types/response';
import { countriesCoordMap } from '@/types/countries';

type ECOption = echarts.ComposeOption<
  | MapSeriesOption
  | GeoComponentOption
  | EffectScatterSeriesOption
  | LinesSeriesOption
  | TooltipComponentOption
>;

const worldUserMap = ref<HTMLElement>();

const option = reactive<ECOption>({
  backgroundColor: '#f0f4f8', // 浅灰蓝色背景，更明亮
  geo: {
    map: 'WorldMap',
    aspectScale: 0.85,
    roam: false,
    zoom: 1.3,
    silent: true,
    z: 1,
    itemStyle: {
      areaColor: '#d1e3f0', // 浅蓝色区域填充
      borderColor: '#a8c0df', // 柔和蓝边框
      borderWidth: 0.7,
      shadowColor: 'rgba(0, 0, 0, 0.1)',
      shadowBlur: 5,
      shadowOffsetX: 1,
      shadowOffsetY: 1
    },
    emphasis: {
      itemStyle: {
        areaColor: '#5a9bf6', // 鼠标悬停时亮蓝色
        borderColor: '#3a7be0',
        borderWidth: 1.2,
        shadowBlur: 15,
        shadowColor: 'rgba(90,155,246,0.7)'
      },
      label: {
        show: false
      }
    }
  },
  tooltip: {
    trigger: 'item',
    backgroundColor: 'rgba(60, 60, 60, 0.85)',
    textStyle: { color: '#fff' },
    padding: [8, 12],
    borderRadius: 6,
    formatter: (params: any) => {
      if (params.seriesType === 'effectScatter') {
        return `<strong>${params.name}</strong>`;
      }
      return null;
    }
  },
  series: [
    {
      name: '用户',
      type: 'map',
      map: 'WorldMap',
      aspectScale: 0.85,
      zoom: 1.3,
      zlevel: 1,
      itemStyle: {
        areaColor: '#aac7f8', // 柔和蓝色
        borderColor: '#e3efff', // 更亮边框
        borderWidth: 0.7,
        shadowColor: 'rgba(0, 0, 0, 0.05)',
        shadowBlur: 4
      },
      emphasis: {
        itemStyle: {
          areaColor: '#4091ff',
          borderColor: '#4091ff',
          borderWidth: 1
        },
        label: { show: false }
      },
      select: {
        itemStyle: {
          areaColor: '#7db7ff',
          borderColor: '#a5d1ff',
          borderWidth: 0.5
        },
        label: { show: false }
      },
      label: { show: false },
      tooltip: { show: false }
    },
    {
      type: 'effectScatter',
      zlevel: 3,
      coordinateSystem: 'geo',
      animation: true,
      itemStyle: {
        color: '#ff6b00', // 橙色更柔和
        shadowBlur: 10,
        shadowColor: 'rgba(255,107,0,0.6)'
      },
      symbolSize: 7,
      rippleEffect: {
        period: 6,
        number: 4,
        scale: 6,
        brushType: 'stroke'
      },
      data: [],
      tooltip: {
        formatter: '{b}'
      }
    },
    {
      type: 'lines',
      zlevel: 2,
      effect: {
        show: true,
        period: 4,
        trailLength: 0.6,
        color: '#ff6b00',
        symbol: 'arrow',
        symbolSize: 4
      },
      lineStyle: {
        color: '#ff6b00',
        width: 0.3,
        opacity: 0.35,
        curveness: 0.25
      },
      data: [],
      tooltip: { show: false }
    }
  ]
});

echarts.use([MapChart, EffectScatterChart, LinesChart]);
const { showLoading, hideLoading } = useEcharts({ dom: worldUserMap, option });
showLoading();
echarts.registerMap('WorldMap', map as any);
hideLoading();

const useStore = useAppStore();
useStore
  .setTotalCount('statUser')
  .then(({ xaxis, yaxis }) => {
    if (Array.isArray(option.series)) {
      const data: {
        name: string;
        value: number;
        coords: [number, number][];
      }[] = [];
      xaxis.forEach((item, index) => {
        const countryName = countryObj[item];
        if (countriesCoordMap[countryName]) {
          data.push({
            name: countryName,
            value: yaxis[index],
            coords: [countriesCoordMap['广东'], countriesCoordMap[countryName]]
          });
        }
      });
      option.series[2] && (option.series[2].data = data);
      option.series[1].data = data.map((item) => ({
        name: item.name,
        value: [...item.coords[1], item.value]
      }));
    }
  })
  .catch(() => null);
</script>

<style scoped>
.world-map-card {
  border-radius: 14px;
  box-shadow: 0 6px 20px rgba(100, 120, 160, 0.15);
  background: #ffffff;
  padding: 20px;
  height: 520px;
  display: flex;
  flex-direction: column;
  user-select: none;
  transition: box-shadow 0.3s ease;
}

.world-map-card:hover {
  box-shadow: 0 10px 30px rgba(100, 120, 160, 0.3);
}

.world-map-container {
  flex: 1;
  width: 100%;
  height: 100%;
  border-radius: 12px;
  background-color: #e9f0fa; /* 更浅的蓝灰背景 */
  box-shadow: inset 0 0 20px rgba(160, 180, 220, 0.3);
  transition: filter 0.3s ease, box-shadow 0.3s ease;
  filter: drop-shadow(0 0 4px rgba(0, 0, 0, 0.05));
}

.world-map-container:hover {
  filter: drop-shadow(0 0 12px #ff6b0077);
  box-shadow: inset 0 0 30px rgba(255, 107, 0, 0.15);
}

@media (max-width: 768px) {
  .world-map-card {
    height: 400px;
    padding: 16px;
  }
}
</style>
