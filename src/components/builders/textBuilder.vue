<template>
  <div class="relative flex w-full">
    <textarea ref="textarea" class="w-full max-md:!text-[18px] max-md:!leading-[22px]" @input="handleInput" v-model="value" @focus="handleFocus"  type="text" />
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, defineEmits } from 'vue';

interface TextBuilder {
  content: string;
  isActive: boolean;
}

const props = defineProps<TextBuilder>();

const textarea = ref(null);

const defaultValue = props.content || "Add custom text";
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
    padding: 0px;
    background-color: transparent;
    resize: none;
    min-height: 50px;
    line-height: 25px;
    font-size: 25px;
    &:focus, &:hover {
      outline: 1px solid var(--color-primary);
    }
  }

  .is-active textarea {
    padding-left: 10px;
    outline: 1px solid var(--color-primary);
  }
</style>