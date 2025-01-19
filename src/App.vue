<template>
  <h1 class="flex pr-[10px] pl-[10px] justify-center items-center text-center title pt-[10px] !text-[30px] uppercase max-md:!text-[21px]  max-sm:!text-[17px]"><i class="fa fa-object-group mr-[20px]"></i>Simple Drag & Drop Landing Page Builder</h1>
  <div id="main-container" class="flex w-full">
    <div id="blocks-container"
      class="flex max-w-[350px] w-full shadow-lg gap-5 flex-col p-[20px] pt-[50px] max-md:w-[100px] max-lg:w-[200px]">
      <div class="sticky top-[0px] left-[0px]">
        <h1 class="max-md:hidden max-lg:!text-[22px] max-lg:!mb-[20px]">Builder Blocks</h1>

        <div id="blocks" class="w-full h-[100px] flex flex-wrap justify-center items-center gap-5">
          <ElementBlock v-for="type in elementsType"
            :type="type"
            @dragstart="onDragStartBlock"
            @click="onClickBlock"
          />
        </div>
      </div>
    </div>

    <div
      id="builder-container"
      class="flex w-full p-[20px] pt-[50px] flex-col gap-1"
      @dragover.prevent
      @drop="onDropBlock"
    >

      <h1 class="mb-[40px] max-md:!text-[20px] max-lg:!text-[22px]">Builder Area</h1>
      <div
        v-if="builderBlocks.length === 0"
        class="max-md:!text-[15px] w-full xl:max-w-[60%] mr-auto ml-auto flex items-center justify-center empty-builder text-center py-20 border-dashed border-2 text-[20px] mt-auto mb-auto h-[90%]"
      >
        Drop Or Click Blocks To Add Here
      </div>
      <BuilderBlock
        v-for="(block, index) in builderBlocks"
        :key="block.id"
        :blockId="block.id"
        :type="block.type"
        :index="index"
        :content="block.content"
        :isActive="selectedId == block.id"
        :class="{ 'is-active':selectedId == block.id, 'opacity-50 dragging': draggedIndex === index, 'add-above':placeholderIndex === index && draggedIndex !== null && index < draggedIndex, 'add-below':placeholderIndex === index && draggedIndex !== null && index > draggedIndex }"
        @activate="setActive"
        @inactivate="setInactive"
        @input="changeContent"
        @duplicate="handleDuplication"
        @delete="handleDelete"
        @dragstart="onDragStartBuilder(index)"
        @dragover="onDragOver(index)"
        @drop="onDropBuilder(index)"
      />
    </div>

    <div v-if="builderBlocks.length > 0" @click="handleSave" class="cursor-pointer fixed bottom-[50px] right-[50px] rounded save-btn p-[15px] z-[10]">
      <i class="fa fa-download"></i>
      Save to JSON
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
    draggedBlockType.value = null;
  }
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

const onDragStartBuilder = (index: number) => {
  draggedIndex.value = index;
  draggedBlockType.value = null;
  placeholderIndex.value = null;
};

const onDragOver = (index: number) => {
  if (draggedIndex.value == null) return;
  if (placeholderIndex.value !== index) {
    placeholderIndex.value = index;
  }
};

const onDropBuilder = (index: number) => {
  if (!draggedBlockType.value) {
    if (draggedIndex.value !== null && draggedIndex.value !== index) {
      const currentBlock = builderBlocks.value[draggedIndex.value];
      builderBlocks.value.splice(draggedIndex.value, 1);
      builderBlocks.value.splice(index, 0, currentBlock);
    }

    draggedIndex.value = null;
    placeholderIndex.value = null;
  }
};

const handleSave = () => {
  const data = JSON.parse(JSON.stringify(builderBlocks.value));
  console.log('The following is the JSON data for this landing Page:');
  console.log(JSON.stringify({ data }));
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

  .save-btn {
    background-color: var(--color-accent);
    color: var(--color-white);
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

  .add-below {
    box-shadow: 0 2px 0 0 red;
  }

  .add-above {
    box-shadow: 0 -2px 0 0 red;
  }
</style>