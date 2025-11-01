<script lang="ts" setup>
import { computed, ref } from 'vue';
import type { HTMLAttributes, PropType } from 'vue';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import Eye from '@/components/shared/icons/Eye.vue';
import EyeOff from '@/components/shared/icons/EyeOff.vue';

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

const showPassword = ref(false);

const model = computed({
  get: () => props.modelValue,
  set: (value: string | number) => emit('update:modelValue', value),
});

const inputType = computed(() => {
  if (props.type === 'password') {
    return showPassword.value ? 'text' : 'password';
  }
  return props.type;
});

const togglePasswordVisibility = () => {
  showPassword.value = !showPassword.value;
};
</script>

<template>
  <div class="relative h-full max-h-12 flex-1">
    <Input
      :id="id"
      v-model="model"
      :type="inputType"
      :placeholder="floating ? '' : ' '"
      :error="error"
      class="peer pr-10"
      :class="[{ 'border-red-500': error }, background, props.class]"
      :disabled="props.disabled"
    />
    <Label
      :for="id"
      class="peer-focus:secondary peer-focus:dark:secondary absolute start-2 top-2 z-10 origin-[0] -translate-y-4 scale-75 transform px-2 text-sm text-gray-500 duration-300 peer-placeholder-shown:top-1/2 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:scale-100 peer-focus:top-2 peer-focus:-translate-y-4 peer-focus:scale-75 peer-focus:px-2 dark:bg-background rtl:peer-focus:left-auto rtl:peer-focus:translate-x-1/4 cursor-text rounded-md"
      :class="background"
    >
      {{ label }}
    </Label>
    <button
      v-if="type === 'password'"
      type="button"
      @click="togglePasswordVisibility"
      class="absolute right-3 top-1/2 -translate-y-1/2 text-gray-500 hover:text-gray-700 focus:outline-none focus:text-gray-700 transition-colors duration-200"
    >
      <Eye v-if="!showPassword" class="size-4" />
      <EyeOff v-else class="size-4" />
    </button>
    <p v-if="error" class="absolute top-8.5 pl-1 left-0 text-xs text-red-500">
      {{ error }}
    </p>
  </div>
</template>
