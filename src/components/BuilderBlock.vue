<template>
  <div
    class="p-4 relative bg-white shadow rounded mb-4 flex items-center justify-between builder-block"
    @click="emitEvent('activate')"
    @duplicate="emitEvent('duplicate')"
    @delete="emitEvent('delete')"
  >
    <component :isActive="isActive" @input="changeContent" :content="content" :is="builderCommp" />

  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, defineEmits, computed } from 'vue';
import ImageBuilder from './builders/imageBuilder.vue';
import TextBuilder from './builders/textBuilder.vue';
import type { DraggableTypes } from '@/typescript/index.ts';

interface BuilderBlock {
  type: DraggableTypes;
  content: string;
  blockId: string;
  index: number;
  isActive: boolean;
}

const builderMap: Record<DraggableTypes, any> = {
  image: ImageBuilder,
  text: TextBuilder,
};

const props = defineProps<BuilderBlock>();

const images = [];

const currentContent = ref<string>(props.content);

const builderCommp = computed(() => {
  return builderMap[props.type];
});

const showExtraActions = computed(() => {
  return props.type == 'image' && props.isActive;
});

type events = 'dragstart' | 'drop' |'dragend' | 'dragover' | 'input' | 'activate' | 'duplicate' | 'delete';

const emit = defineEmits(['dragstart', 'drop', 'dragend', 'dragover', 'input', 'activate', 'duplicate', 'delete'])

const emitEvent = (event: events) => {
  emit(event, { type: props.type, blockId: props.blockId, index: props.index });
}

const changeContent = (content: string) => {
  emit('input', { content, index: props.index })
};

</script>

<style scoped lang="scss">
</style>