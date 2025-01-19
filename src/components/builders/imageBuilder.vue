<template>
  <div>
    <img :src="imageSrc" />
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
</style>