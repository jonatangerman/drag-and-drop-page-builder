<template>
  <div
    class="relative bg-transparent flex builder-block ml-auto mr-auto"
    :class="`${type}-builder`"
    @click="emitEvent('activate')"
    @duplicate="emitEvent('duplicate')"
    @delete="emitEvent('delete')"
  >
    <div v-show="isActive"
      class="z-[9] drag-handler h-[52px] w-[40px] bottom-auto left-[-39px] absolute flex items-center justify-center rounded-tl-[4px] rounded-bl-[4px]"
      draggable="true"
      @click.stop
      @dragstart="emitEvent('dragstart')"
      @dragover.prevent="emitEvent('dragover')"
      @drop="emitEvent('drop')"
      @dragend="emitEvent('dragend')"
    >
      <i class="fa fa-bars"></i>
    </div>

    <div @click.stop="showImageSelector" v-show="showExtraActions" class="z-[9] extra-actions absolute top-[-30px] flex justify-center w-full">
      <div class="image-selector-btn h-[30px] flex justify-center items-center !cursor-pointer rounded-tl-[4px] rounded-tr-[4px]">
        <i class="fa fa-image"></i>
      </div>
    </div>

    <component class="z-[9]" :isActive="isActive" @input="changeContent" :content="content" :is="builderCommp" @activate="emitEvent('activate')" />

    <div v-show="isActive"
      class="action-container absolute top-auto bottom-auto right-[-39px] z-[9] rounded-br-[4px] rounded-tr-[4px]"
    >
      <div @click.stop="emitEvent('duplicate')" class="duplicate-btn">
        <i class="fa fa-clone"></i>
      </div>
      <div @click.stop="emitEvent('delete')" class="delete-btn ">
        <i class="fa fa-trash"></i>
      </div>
    </div>

    <div v-show="isImageSelectorVisible" class="rounded flex w-full flex-col image-selector-modal z-[20] absolute bottom-[0px] left-[0px] p-[10px]">
      <h2 class="mb-[10px]">Choose Block Image</h2>
      <div class="images-container justify-center w-full flex flex-wrap gap-[2px]">
        <img :class="{'selected': currentContent == img }" @click.stop="setCurrentContent(img)" class="cursor-pointer flex-none" v-for="(img, index) in presetImages" :src="img" :alt="`image-${index}`">
      </div>
      <div class="modal-actions w-full flex gap-[10px] justify-end">
        <div @click.stop="closeImageSelector" class="cursor-pointer p-3">Close</div>
        <div @click="chooseImage" class="cursor-pointer p-3">Select</div>
      </div>
    </div>
    <div @click.stop="emitEvent('inactivate')" v-if="isActive" class="blocker bg-transparent z-[1]"></div>
    <div @click.stop="closeImageSelector" v-if="isImageSelectorVisible" class="bg-black bg-opacity-20 blocker z-[19]"></div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, defineEmits, computed } from 'vue';
import ImageBuilder from './builders/imageBuilder.vue';
import TextBuilder from './builders/textBuilder.vue';
import type { DraggableTypes, BuilderBlockParams } from '@/typescript/index.ts';

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

const presetImages = ['https://fastly.picsum.photos/id/574/1088/520.jpg?hmac=vjAANsnGcTUEI19eACf6ImUAmQwaIJe3CfUA5Oad-CA', 'https://fastly.picsum.photos/id/724/1088/520.jpg?hmac=bX8kVoyQ6FpqwtegSZk7xqGEQiLdSq-oAdo-Ifznyms', 'https://fastly.picsum.photos/id/684/1088/520.jpg?hmac=H9gPzjqo5rISL2a3p5Ws6w0DuVMtBalEV6Hswg4c9Tk', 'https://fastly.picsum.photos/id/498/1088/520.jpg?hmac=T8qWtY1qe-0OUeztFlU_TRG51Spqa9aRv-OrsEfwXBk'];

const currentContent = ref<string>(props.content);

const builderCommp = computed(() => {
  return builderMap[props.type];
});

const isImageSelectorVisible = ref<boolean>(false);

const showExtraActions = computed(() => {
  return props.type == 'image' && props.isActive;
});

type events = 'dragstart' | 'drop' |'dragend' | 'dragover' | 'input' | 'activate' | 'duplicate' | 'delete' | 'inactivate';

const emit = defineEmits(['dragstart', 'drop', 'dragend', 'dragover', 'input', 'activate', 'duplicate', 'delete', 'inactivate'])

const emitEvent = (event: events) => {
  emit(event, { blockId: props.blockId, index: props.index, content: props.content } as BuilderBlockParams);
}

const changeContent = (content: string) => {
  emit('input', { content, index: props.index, blockId: props.blockId } as BuilderBlockParams)
};

const showImageSelector = () => {
  isImageSelectorVisible.value = true;
  currentContent.value = presetImages[0];
}

const setCurrentContent = (img: string) => {
  currentContent.value = img;
}

const closeImageSelector = () => {
  isImageSelectorVisible.value = false;
  currentContent.value = '';
}

const chooseImage = () => {
  changeContent(currentContent.value);
  closeImageSelector();
}


</script>

<style scoped lang="scss">
  .blocker {
    position: fixed;
    top: 0px;
    left: 0px;
    width: 100%;
    height: 100%;
  }

  .builder-block {
    transition: width 0.2s ease;
    width: 100%;

    &.is-active {
      width: calc(100% - 80px);
    }
  }

  .text-builder {
    min-height: 30px;
  }

  .drag-handler, .action-container, .image-selector-btn {
    background-color: var(--color-primary);
    color: var(--color-white);
    top: -1px;
    width: 38px;
    cursor: row-resize;
  }

  .images-container img {
    width: calc(50% - 10px);

    &.selected {
      outline: 2px solid var(--color-secondary);
    }
  }

  .image-selector-modal {
    background-color: var(--color-black);
    color: var(--color-white);
  }

  .action-container {
    div {
      display: flex;
      flex: none;
      justify-content: center;
      align-items: center;
      height: 26.5px;
      cursor: pointer;
      border-bottom: 1px solid var(--color-white);
    }
  }
</style>