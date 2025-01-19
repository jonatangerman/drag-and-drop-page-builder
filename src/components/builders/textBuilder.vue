<template>
  <div class="relative flex w-full">
    <textarea ref="textarea" class="w-full" @input="handleInput" v-model="value" @focus="handleFocus"  type="text" />
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, defineEmits } from 'vue';

interface TextBuilder {
  content: string;
  isActive: boolean;
}

defineProps<TextBuilder>();

const textarea = ref(null);

const defaultValue = "Insert the text you want!";
const value = ref<string>(defaultValue);

const handleInput = () => {
  if (textarea.value) {
    const input = textarea.value as HTMLInputElement;
    input.style.height = "auto";
    input.style.height = `${input.scrollHeight}px`;
  }
  emit('input', value.value);
}

const handleFocus = () => {
  emit('activate');
}

const emit = defineEmits(['input', 'activate'])
</script>

<style scoped lang="scss">
  textarea {
    resize: none;
    min-height: 50px;
    font-size: 30px;
    padding: 0px 20px;
    &:focus {
      outline: 2px solid var(--color-accent);
    }
  }
</style>