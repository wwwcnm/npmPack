<template>
  <div class="swiper">
    <div class="banner" ref="banner">
      <div
        class="banner-tiem"
        v-for="item in swriperList.swipes"
        :key="item.id"
        :style="{ background: item.color }"
      >
        {{ item.text }}
      </div>
    </div>
    <div class="pageniaition">
      {{ swriperList.count }}/{{ swriperList.swipes.length - 2 }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, reactive } from "vue";
// 模拟数据
const props = defineProps<{
  // 传递过来的数据
  swriperList: {
    count: string;
    swipes: {
      id: string;
      text: string;
      color: string;
    }[];
  };
}>();
const banner = ref<HTMLDivElement>();
const getBanner = () => banner.value!;

interface iDrag {
  disx: number;
  x: number;
  now: number;
}

const Drag = reactive<iDrag>({
  x: 0,
  disx: 0,
  now: 1,
});

const onMove = (ev: TouchEvent) => {
  Drag.x = ev.changedTouches[0].pageX - Drag.disx;
  getBanner().style.transform = `translate(${Drag.now * -100 + Drag.x / 10}vw,0px)`;
};
const onEnd = () => {
  getBanner().ontouchmove = null;
  getBanner().ontouchend = null;
  getBanner().style.transition = `.3s ease-in transform`;
  if (Drag.x < -50) Drag.now++;
  if (Drag.x > 50) Drag.now--;
  if (Drag.x === 0) {
    getBanner().style.transform = ``;
  } else {
    getBanner().style.transform = `translate(${Drag.now * -100}vw,0px)`;
  }
  //   Drag.disx = 0;
  Drag.x = 0;
  props.swriperList.count = props.swriperList.swipes[Drag.now].text;
};
const onStart = (ev: TouchEvent) => {
  getBanner().style.transition = ``;
  Drag.x = 0;
  if (Drag.now === 5) Drag.now = 1;
  if (Drag.now === 0) Drag.now = 4;
  Drag.disx = ev.changedTouches[0].pageX;
  getBanner().ontouchmove = onMove;
  getBanner().ontouchend = onEnd;
};

onMounted(() => {
  getBanner().ontouchstart = onStart;
});
</script>

<style scoped lang="scss">
.swiper {
  width: 100vw;
  height: 300px;
  overflow: hidden;
  position: relative;

  .banner {
    width: 600vw;
    height: 300px;
    display: flex;
    transform: translate(-100vw, 0px);

    .banner-tiem {
      width: 100vw;
      height: 300px;
      text-align: center;
      line-height: 300px;
      font-size: 80px;
    }
  }
}

.pageniaition {
  width: 50%;
  height: 30px;
  position: absolute;
  left: 25%;
  bottom: 0;
  font-size: 25px;
  margin: auto;
  z-index: 999;
  text-align: center;
  line-height: 30px;

  .pageniaition_item {
    width: 25px;
    height: 25px;
    background-color: black;
    border-radius: 50px;
  }
}
</style>
