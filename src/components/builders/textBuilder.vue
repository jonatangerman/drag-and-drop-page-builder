<template>
  <div>
    <input @input="handleInput" v-model="value" :disabled="!isActive" type="text" />
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, defineEmits } from 'vue';

interface TextBuilder {
  content: string;
  isActive: boolean;
}

defineProps<TextBuilder>();

const defaultValue = "Insert the text you want!";
const value = ref<string>(defaultValue);

const handleInput = () => {
  emit('input', value.value);
}

const emit = defineEmits(['input'])
</script>

<style scoped lang="scss">
</style>