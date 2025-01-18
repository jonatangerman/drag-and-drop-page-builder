<template>
  <div class="p-2 rounded-[4px] flex-none gap-[10px] cursor-pointer justify-center flex items-center h-[50px] w-[auto] pr-[30px] pl-[30px]" draggable="true">
    <i :class="icon"></i> {{ text }}
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref } from 'vue';

interface DraggableBlock {
  type: 'text' | 'image'
}

const props = defineProps<DraggableBlock>();
const icon = ref<string>('');
const text = ref<string>('');

const init = () => {
  switch (props.type) {

    case 'text':
      icon.value = 'fa fa-text-width'
      text.value = 'Text'
    break;

    case 'image':
      icon.value = 'fa fa-image'
      text.value = 'Image'
    break;

    default:
      return '';
  }
}

  init();
</script>

<style scoped lang="scss">
  div {
    color: var(--color-white);
    background-color: var(--color-black);
  }
</style>