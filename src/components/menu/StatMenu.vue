<template>
  <div class="grid grid-cols-2 md:grid-cols-3 xl:grid-cols-4 gap-4 p-4">
    <stat-total-card
      v-for="item in statData"
      :key="item.title"
      :title="item.title"
      :img="item.img"
      :count="item.count"
      :color="item.color"
      :show-border="item.dataId === selectId"
      class="transition-transform duration-200 hover:scale-105 hover:shadow-md cursor-pointer"
      @click="handleClick(item)"
    />
  </div>
</template>

<script lang="ts" setup>
import { STAT } from '@/types';
import { reactive, Ref, ref } from 'vue';
import StatTotalCard from '@/components/statTotalCard/StatTotalCard.vue';
import { useAppStore } from '@/store';
import userIcon from '@/assets/user1.png';
import deviceIcon from '@/assets/device1.png';
import serviceIcon from '@/assets/service1.png';
import dollarIcon from '@/assets/YUAN.png';

const emit = defineEmits<{
  (e: 'click', { dataId, color }: { dataId: STAT; color: string }): void;
}>();

const statList: STAT[] = [
  'statUser',
  'statDevice',
  'statCloudStorageService',
  'statCloudStorageAmount'
];

const statData: {
  dataId: STAT;
  img: any;
  title: string;
  count: number;
  color: string;
}[] = reactive([
  {
    dataId: 'statUser',
    img: userIcon,
    title: '客户',
    count: 0,
    color: '#CFEFFF '
  },
  {
    dataId: 'statDevice',
    img: deviceIcon,
    title: '终端',
    count: 0,
    color: '#CFEFFF '
  },
  {
    dataId: 'statCloudStorageService',
    img: serviceIcon,
    title: '订阅',
    count: 0,
    color: '#CFEFFF '
  },
  {
    dataId: 'statCloudStorageAmount',
    img: dollarIcon,
    title: '盈利(亿)',
    count: 0,
    color: '#CFEFFF '
  }
]);

const selectId: Ref<STAT> = ref('statUser');

const useStore = useAppStore();

function handleClick({ dataId, color }: { dataId: STAT; color: string }) {
  selectId.value = dataId;
  emit('click', { dataId, color });
}

useStore
  .setTotalList(statList)
  .then((res) => {
    for (let i = 0; i < res.length; i++) {
      statData[i].count = res[i];
    }
  })
  .catch(() => null);
</script>
