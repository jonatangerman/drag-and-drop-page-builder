<template>
  <div
    class="relative bg-white shadow rounded flex builder-block ml-auto mr-auto"
    :class="`${type}-builder`"
    @click="emitEvent('activate')"
    @duplicate="emitEvent('duplicate')"
    @delete="emitEvent('delete')"
  >
    <div v-show="isActive"
      class="z-[9] drag-handler top-[-2px] h-[54px] w-[40px] bottom-auto left-[-40px] absolute flex items-center justify-center"
      draggable="true"
      @click.stop
      @dragstart="emitEvent('dragstart')"
      @dragover.prevent="emitEvent('dragover')"
      @drop="emitEvent('drop')"
      @dragend="emitEvent('dragend')"
    >
      <i class="fa fa-bars"></i>
    </div>

    <div @click.stop v-show="showExtraActions" class="z-[9] extra-actions absolute right-auto left-auto top-[50px]">
      <div class="image-selector-btn"></div>
    </div>

    <component class="z-[9]" :isActive="isActive" @input="changeContent" :content="content" :is="builderCommp" @activate="emitEvent('activate')" />

    <div v-show="isActive"
      class="action-container absolute top-auto bottom-auto right-[-40px] z-[9]"
    >
      <div @click.stop class="duplicate-btn">
        <i class="fa fa-clone"></i>
      </div>
      <div @click.stop class="delete-btn">
        <i class="fa fa-trash"></i>
      </div>
    </div>

    <div v-show="showImageSelector" class="image-selector-modal z-[20]">
      <div class="images-container">
        <img v-for="(img, index) in images" :src="img" :alt="`image-${index}`">
      </div>
      <div class="modal-actions">
        <div>Close</div>
        <div>Select</div>
      </div>
    </div>
    <div @click.stop="emitEvent('inactivate')" v-if="isActive" class="blocker"></div>
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

const images = ['https://fastly.picsum.photos/id/467/1088/520.jpg?hmac=cLhQAUYnNTN852jfTo6gxc_FQGCZr5E_D0eNppkW2lI', 'https://fastly.picsum.photos/id/724/1088/520.jpg?hmac=bX8kVoyQ6FpqwtegSZk7xqGEQiLdSq-oAdo-Ifznyms', 'https://fastly.picsum.photos/id/684/1088/520.jpg?hmac=H9gPzjqo5rISL2a3p5Ws6w0DuVMtBalEV6Hswg4c9Tk', 'https://fastly.picsum.photos/id/498/1088/520.jpg?hmac=T8qWtY1qe-0OUeztFlU_TRG51Spqa9aRv-OrsEfwXBk'];

const currentContent = ref<string>(props.content);

const builderCommp = computed(() => {
  return builderMap[props.type];
});

const showImageSelector = ref<boolean>(false);

const showExtraActions = computed(() => {
  return props.type == 'image' && props.isActive;
});

type events = 'dragstart' | 'drop' |'dragend' | 'dragover' | 'input' | 'activate' | 'duplicate' | 'delete' | 'inactivate';

const emit = defineEmits(['dragstart', 'drop', 'dragend', 'dragover', 'input', 'activate', 'duplicate', 'delete', 'inactivate'])

const emitEvent = (event: events) => {
  console.log("INACT ", event);
  emit(event, { blockId: props.blockId, index: props.index, content: props.content } as BuilderBlockParams);
}

const changeContent = (content: string) => {
  console.log("CONTENT ", content);
  emit('input', { content, index: props.index, blockId: props.blockId } as BuilderBlockParams)
};

</script>

<style scoped lang="scss">
  .blocker {
    position: fixed;
    top: 0px;
    left: 0px;
    width: 100%;
    height: 100%;
    background-color: transparent;
    z-index: 1;

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
    height: 50px;
  }

  .image-builder {
    min-height: 520px;
  }

  .drag-handler, .action-container {
    background-color: var(--color-black);
    color: var(--color-white);
    top: -2px;
    width: 38px;
    cursor: row-resize;
  }

  .action-container {
    div {
      display: flex;
      flex: none;
      justify-content: center;
      align-items: center;
      height: 27.5px;
      cursor: pointer;
      border-bottom: 1px solid var(--color-white);
    }
  }
</style>