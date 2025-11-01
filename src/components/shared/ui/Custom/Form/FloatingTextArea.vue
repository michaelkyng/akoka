<script lang="ts" setup>
import { computed } from 'vue';
import type { HTMLAttributes, PropType } from 'vue';
import { Textarea } from '@/components/ui/textarea';
import { Label } from '@/components/ui/label';

const props = defineProps({
  id: {
    type: String,
    required: true,
  },
  modelValue: {
    type: [String, Number],
    default: '',
  },
  label: {
    type: String,
    required: true,
  },
  type: {
    type: String,
    default: 'text',
  },
  placeholder: {
    type: String,
    default: '',
  },
  error: {
    type: [String, Object],
    default: null,
  },
  class: {
    type: String as PropType<HTMLAttributes['class']>,
    default: '',
  },
  disabled: {
    type: Boolean as PropType<HTMLInputElement['disabled']>,
    default: false,
  },
  floating: {
    type: Boolean,
    default: true,
  },
  background: {
    type: String,
    default: 'bg-background',
  },
});

const emit = defineEmits(['update:modelValue']);

const model = computed({
  get: () => props.modelValue,
  set: (value: string | number) => emit('update:modelValue', value),
});
</script>

<template>
  <div class="relative h-full max-h-fit flex-1 overflow-visible">
    <Textarea
      :id="id"
      v-model="model"
      :placeholder="floating ? placeholder : ' ' "
      :error="error"
      class="peer pr-10"
      :class="[{ 'border-red-500': error }, background, props.class]"
      :disabled="props.disabled"
    />
    <Label
      :for="id"
      class="peer-focus:secondary peer-focus:dark:secondary absolute top-0 z-0 origin-[0] -translate-y-2 scale-75 transform px-2 text-sm text-gray-500 duration-300 peer-placeholder-shown:top-1/2 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:scale-100 peer-focus:top-2 peer-focus:-translate-y-4 peer-focus:scale-75 peer-focus:px-2 dark:bg-background rtl:peer-focus:left-auto rtl:peer-focus:translate-x-1/4 cursor-text rounded-md"
      :class="background"
    >
      {{ label }}
    </Label>
    <p v-if="error" class="absolute top-8.5 pl-1 left-0 text-xs text-red-500">
      {{ error }}
    </p>
  </div>
</template>
