<template>
  <div class="sm:hidden">
    <div
      :class="[
        'hidden fixed top-0 left-0 w-full h-full bg-black z-50 transition-all duration-300',
        { 'open-dialog': isOpen },
      ]"
    >
      <div class="overlay" @click="onClose">
        <img :src="slides[currentIndex].url" alt="" />
      </div>
      <div class="content h-[90%]">
        <slot name="mobile-carousel"></slot>
      </div>
    </div>
  </div>
  <div class="hidden sm:block">
    <div
      :class="[
        'hidden fixed top-0 left-0 w-full h-full bg-black z-50 transition-all duration-300',
        { 'open-dialog': isOpen },
      ]"
    >
      <div class="overlay" @click="onClose"></div>
      <div class="content w-[90%] h-[90%] sm:w-2/3 sm:h-[70%]">
        <slot name="desktop-carousel"></slot>
      </div>
    </div>
  </div>
</template>

<script setup>
import { watch, onUnmounted } from "vue";
import { ref, defineProps } from "vue";

const props = defineProps({
  isOpen: {
    type: Boolean,
    required: true,
  },
  onClose: {
    type: Function,
    required: true,
  },
  slides: {
    type: Array,
    required: true,
  },
  currentIndex: {
    type: Number,
    required: true,
  },
});

watch(
  () => props.isOpen,
  (newValue) => {
    if (newValue) {
      document.body.style.overflow = "hidden";
    } else {
      document.body.style.overflow = "visible";
    }
  },
  { immediate: true }
);

// Reset overflow on component unmount
onUnmounted(() => {
  document.body.style.overflow = "visible";
});
</script>

<style>
.visible {
  opacity: 1;
}

.open-dialog {
  display: block;
  transition: all;
  transition-duration: 400ms;
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  filter: blur(12px);
}

.overlay img {
  width: 100%;
  height: 100%;
}

.content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  /* padding: 20px; */
  border: 1px solid transparent;
  border-radius: 20px;
}

@media screen and (max-width: 768px) {
  .content {
    width: 90%;
  }
}
</style>
