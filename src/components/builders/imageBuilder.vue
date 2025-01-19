<template>
  <div class="flex w-full image-container items-center justify-center">
    <img v-if="!content" class='is-empty' :src="imageSrc" />
    <img v-else :src="imageSrc" />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';

interface BuilderBlock {
  content: string;
}

const props = defineProps<BuilderBlock>();

const imageSrc = computed(() => {
  return props.content ? props.content : '/no-image.png'
})

</script>

<style scoped lang="scss">
  .is-active .image-container {
    outline: 1px solid var(--color-primary);
  }

  .image-container {
    width: 100%;
    height: 0;
    padding-top: calc(520 / 1088 * 100%);
    position: relative;
    background-color: var(--color-gray);

    &:hover {
      outline: 1px solid var(--color-primary);
    }
  }

  img {
    top: 0px;
    position: absolute;
    left: 0px;
    width: 100%;
  }

  .is-empty {
    max-width: 100px;
    max-height: 100px;
    width: 15%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
</style>