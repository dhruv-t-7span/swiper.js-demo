<template>
  <button @click="openDialog">Click Here</button>
  <Dialog
    :isOpen="showModal"
    :onClose="closeDialog"
    :slides="slides"
    :currentIndex="currentIndex"
    class=""
  >
    <template #mobile-carousel>
      <Swiper
        v-if="showModal"
        :spaceBetween="30"
        :centeredSlides="true"
        :autoplay="{
          delay: 10000,
          disableOnInteraction: false,
        }"
        :navigation="true"
        :effect="'fade'"
        :modules="modules"
        @autoplayTimeLeft="onAutoplayTimeLeft"
        @activeIndexChange="activeIndexChange"
        class="mySwiper"
      >
        <template #container-end>
          <div class="autoplay-progress-container">
            <div v-for="x in slides" :key="x.id" class="autoplay-progress">
              <div class="progress-bar"></div>
            </div>
          </div>
        </template>
        <SwiperSlide
          v-for="item in slides"
          :key="item.id"
          v-slot="{ isActive }"
        >
          <img
            v-if="item.type === 'image' && isActive"
            :src="item.url"
            alt=""
            class="w-full h-auto rounded-3xl"
          />
          <video
            @click="(e) => e.stopPropagation()"
            v-else-if="item.type == 'video' && isActive"
            video
            autoplay
            muted
            ref="video"
          >
            <source :src="item.url" type="video/mp4" />
          </video>
        </SwiperSlide>
      </Swiper>
    </template>
    <template #desktop-carousel>
      <div class="h-full relative flex gap-6">
        <Swiper
          @swiper="setSwiperInstace"
          :spaceBetween="30"
          :effect="'fade'"
          :modules="modules"
          :navigation="true"
          @activeIndexChange="(swiper) => (activeSlide = swiper.activeIndex)"
          class="mySwiper rounded-3xl !w-3/5"
        >
          <SwiperSlide v-for="slide in slides" :key="slide.id">
            <div class="h-full">
              <img class="w-full" :src="slide.url" />
              <span
                class="bg-black rounded-3xl px-4 py-2 absolute top-3 right-3 text-white"
                >{{ slide.id + "/" + slides.length }}</span
              >
            </div>
          </SwiperSlide>
        </Swiper>
        <div class="overflow-y-auto w-2/5">
          <div class="grid grid-cols-2 gap-4 h-full">
            <div class="relative h-48" v-for="slide in slides" :key="slide.id">
              <img
                v-if="slide.type == 'image'"
                :class="[
                  activeSlide + 1 == slide.id ? 'opacity-100' : 'opacity-50',
                  'object-cover w-full h-full rounded-lg cursor-pointer opacity-50 hover:opacity-100 border border-transparent hover:border-gray-400',
                ]"
                :src="slide.url"
                @click="changeSlide(slide.id - 1)"
                alt=""
              />
              <video
                :class="[
                  activeSlide == slide.id ? 'opacity-100' : 'opacity-50',
                  'object-cover w-full h-full rounded-lg cursor-pointer opacity-50 hover:opacity-100 border border-transparent hover:border-gray-400',
                ]"
                v-else-if="slide.type == 'video'"
                video
                autoplay
                muted
              >
                <source :src="slide.url" type="video/mp4" />
              </video>
            </div>
          </div>
        </div>
      </div>
    </template>
  </Dialog>
</template>

<script setup>
import Dialog from "./components/dialog.vue";
import "swiper/css";
import "swiper/css/pagination";
import "swiper/css/navigation";
import { reactive, ref } from "vue";
import { Swiper, SwiperSlide, useSwiper } from "swiper/vue";
import "swiper/css/effect-fade";
import "./style.css";
import { Autoplay, Pagination, Navigation, EffectFade } from "swiper/modules";
const currentIndex = ref(0);
const activeSlide = ref(1);
const video = ref(null);
const showModal = ref(false);
const swiper = useSwiper();
const swiperInstace = ref();

const setSwiperInstace = (swiper) => {
  swiperInstace.value = swiper;
};

const changeSlide = (index) => {
  swiperInstace.value.slideTo(index);
};

const slides = reactive([
  {
    id: 1,
    type: "image",
    url: "https://cdn.pixabay.com/photo/2020/01/22/07/33/hot-4784635_1280.jpg",
  },
  {
    id: 2,
    type: "image",
    url: "https://cdn.pixabay.com/photo/2017/08/09/11/11/hot-2614220_1280.jpg",
  },
  {
    id: 3,
    type: "image",
    url: "https://cdn.pixabay.com/photo/2017/11/20/13/42/hot-air-ballon-2965604_1280.jpg",
  },
  {
    id: 4,
    type: "image",
    url: "https://cdn.pixabay.com/photo/2017/02/28/23/59/ballon-2107144_1280.jpg",
  },
  {
    id: 5,
    type: "image",
    url: "https://cdn.pixabay.com/photo/2023/08/05/08/18/moon-8170668_1280.jpg",
  },
  {
    id: 6,
    type: "image",
    url: "https://cdn.pixabay.com/photo/2016/11/25/23/15/moon-1859616_1280.jpg",
  },
  // {
  //   id: 7,
  //   type: "video",
  //   url: "https://7span-product.b-cdn.net/59a18bf3-cbc2-43a2-a439-f74e15df00ef.mp4",
  // },
  // {
  //   id: 7,
  //   type: "image",
  //   url: "https://cdn.pixabay.com/photo/2024/05/02/16/22/parrots-8735074_1280.jpg",
  // },
]);

const onAutoplayTimeLeft = (s, time, progress) => {
  const lines = document.querySelectorAll(".autoplay-progress .progress-bar");
  lines.forEach((bar, index) => {
    if (index === s.realIndex) {
      bar.style.width = `${(1 - progress) * 100}%`;
      bar.style.background = "rgba(0, 0, 0, 0.2)";
    } else if (index <= s.realIndex) {
      bar.style.width = "100%";
      bar.style.background = "rgba(0, 0, 0, 0.2)";
    } else {
      bar.style.width = "100%";
      bar.style.background = "white";
    }
  });
};

const activeIndexChange = (swiper) => {
  currentIndex.value = swiper.activeIndex;
};

const modules = [Autoplay, Pagination, Navigation, EffectFade];

const closeDialog = () => {
  showModal.value = false;
};

const openDialog = () => {
  // debugger;
  showModal.value = true;
};
</script>
