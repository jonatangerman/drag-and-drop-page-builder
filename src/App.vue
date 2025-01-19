<template>
  <h1 class="title text-center pt-[10px] !text-[30px] uppercase"><i class="fa fa-object-group mr-[20px]"></i>Simple Drag & Drop Landing Page Builder</h1>
  <div id="main-container" class="flex w-full">
    <div id="blocks-container"
      class="flex max-w-[350px] w-full shadow-lg gap-5 flex-col p-[20px] pt-[50px]">

      <h1>Builder Blocks</h1>

      <div id="blocks" class="w-full h-[100px] flex flex-wrap justify-center items-center gap-5">
        <ElementBlock v-for="type in elementsType"
          :type="type"
          @dragstart="onDragStartBlock"
          @click="onClickBlock"
        />
      </div>
    </div>

    <div
      id="builder-container"
      class="flex w-full p-[20px] pt-[50px] flex-col gap-1"
      @dragover.prevent
      @drop="onDropBlock"
    >

      <h1 class="mb-[40px]">Builder Area</h1>
      <div
        v-if="builderBlocks.length === 0"
        class="flex items-center justify-center empty-builder text-center py-20 border-dashed border-2 text-[20px] mt-auto mb-auto h-[90%]"
      >
        Drop Or Click Blocks To Add Here
      </div>

      <div
        v-for="(block, index) in builderBlocks"
        :key="block.id"
        class="relative"
      >
        <div
          v-if="placeholderIndex === index"
          class="h-16 bg-blue-200 rounded mb-4 builder-placeholder"
        ></div>

        <BuilderBlock 
          :blockId="block.id"
          :type="block.type"
          :index="index"
          :content="block.content"
          :isActive="selectedId == block.id"
          :class="{ 'is-active':selectedId == block.id }"
          @activate="setActive"
          @inactivate="setInactive"
          @input="changeContent"
          @duplicate="handleDuplication"
          @delete="handleDelete"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
  import type { DraggableTypes, BuilderBlockParams } from '@/typescript/index.ts';
  import ElementBlock from '@/components/ElementBlock.vue'
  import BuilderBlock from '@/components/BuilderBlock.vue'
  import { ref } from 'vue';

  const elementsType: DraggableTypes[] = ['text', 'image'];
  const draggedBlockType = ref<DraggableTypes | null>(null);
  const draggedIndex = ref<number | null>(null);
  const placeholderIndex = ref<number | null>(null);

  interface Block {
    id: string;
    type: DraggableTypes;
    content: string;
  }

  const builderBlocks = ref<Block[]>([]);
  const selectedId = ref<string>('');

  const onClickBlock = (type: DraggableTypes) => {
    onDragStartBlock(type);
    onDropBlock();
  }

  const onDragStartBlock = (type: DraggableTypes) => {
    draggedBlockType.value = type;
    draggedIndex.value = null;
    placeholderIndex.value = null;
  }

  const onDropBlock = () => {
  if (draggedBlockType.value) {
    const newBlock = {
      id: crypto.randomUUID(),
      type: draggedBlockType.value,
      content: '',
    };

    builderBlocks.value.splice(
      builderBlocks.value.length,0, newBlock
    );
  }

  draggedBlockType.value = null;
};

const setActive = (params: BuilderBlockParams) => {
  selectedId.value = params.blockId;
}

const changeContent = (params: BuilderBlockParams) => {
  builderBlocks.value[params.index].content = params.content;
}

const setInactive = () => {
  selectedId.value = '';
}

const handleDuplication = (params: BuilderBlockParams) => {
  const current = builderBlocks.value[params.index];
  builderBlocks.value.splice(params.index + 1, 0, { ...current, id: crypto.randomUUID() });
}

const handleDelete = (params: BuilderBlockParams) => {
  builderBlocks.value.splice(params.index, 1);
}

</script>

<style lang="scss" scoped>

  #main-container {
    min-height: calc(100% - var(--title-height));
  }

  h1 {
    color: var(--color-black);
    font-size: 25px;
    font-weight: bold;
    width: 100%;
    padding-bottom: 20px;
    border-bottom: 1px solid var(--color-accent);
  }

  h1.title {
    background-color: var(--color-black);
    color: var(--color-secondary);
    height: var(--title-height);
  }

  #builder-container {
    background-color: white;

    .empty-builder {
      border-color: var(--color-secondary);
      color: var(--color-secondary);
      border-style: dotted;
    }
  }

  #blocks-container {
    border-right: 1px solid var(--color-accent);
    background-color: var(--color-white);
  }
</style>