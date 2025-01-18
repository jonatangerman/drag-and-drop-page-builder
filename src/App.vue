<template>
  <div id="main-container" class="min-h-screen flex w-full">
    <div id="blocks-container"
      class="flex max-w-[350px] w-full shadow-lg gap-5 flex-col p-[20px] pt-[50px]">
      <h1>Draggable Elements</h1>
      <div id="blocks" class="w-full h-[100px] flex flex-wrap justify-center items-center gap-5">
        <DraggableBlock type="text" />
        <DraggableBlock type="image" />
      </div>
    </div>
    <div id="builder-container" class="flex w-full"></div>
  </div>
</template>

<script setup lang="ts">
  import DraggableBlock from '@/components/DraggableBlock.vue'
</script>

<style lang="scss" scoped>

  h1 {
    color: var(--color-black);
    font-size: 20px;
    font-weight: bold;
    width: 100%;
    padding-bottom: 20px;
    border-bottom: 2px solid var(--color-accent)
  }

  #builder-container {
    background-color: var(--color-white);

    .empty-builder {
      border-color: var(--color-secondary);
      color: var(--color-secondary);
      border-style: dotted;
    }
  }

  #blocks-container {
    background-color: var(--color-secondary);
  }
</style>