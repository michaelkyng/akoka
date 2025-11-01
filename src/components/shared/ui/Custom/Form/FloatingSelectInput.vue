<script lang="ts" setup>
import Label from '@/components/ui/label/Label.vue';
import {
  Select,
  SelectTrigger,
  SelectValue,
  SelectContent,
  SelectItem,
} from '@/components/ui/select';
import { computed, ref, watch } from 'vue';

const props = defineProps({
  id: {
    type: String,
    default: () => `select-${Math.random().toString(36).substr(2, 9)}`,
  },
  modelValue: {
    type: [String, Number, Object],
    default: '',
  },
  options: {
    type: Array as () => { label: string; value: string; disabled?: boolean }[],
    required: true,
    validator: (
      value: { label: string; value: string; disabled?: boolean }[]
    ) => {
      return value.every(option => 'label' in option && 'value' in option);
    },
  },
  label: {
    type: String,
    default: '',
  },
  placeholder: {
    type: String,
    default: 'Select an option',
  },
  error: {
    type: String,
    default: '',
  },
});

const emit = defineEmits<{
  'update:modelValue': [value: string | number | object | bigint | null]
}>();
const isOpen = ref(false);
const internalValue = ref<string | number | object | bigint | null>(props.modelValue);

// Watch for external changes to modelValue
watch(() => props.modelValue, (newValue) => {
  internalValue.value = newValue;
}, { immediate: true });

// Watch for internal changes and emit
watch(internalValue, (newValue) => {
  emit('update:modelValue', newValue);
});

const handleValueChange = (value: string | number | object | bigint | null) => {
  internalValue.value = value;
};

const handleOpenChange = (open: boolean) => {
  isOpen.value = open;
};

// shouldFloat logic - checking both internal value and prop
const shouldFloat = computed(() => {
  const currentValue = internalValue.value || props.modelValue;
  const hasValue = currentValue !== '' && 
                  currentValue !== null && 
                  currentValue !== undefined;
  
  
  return isOpen.value || hasValue;
});
</script>

<template>
  <div class="relative w-full cursor-pointer">
    <Select
      :model-value="internalValue"
      @update:model-value="handleValueChange"
      @update:open="handleOpenChange"
    >
      <SelectTrigger
        :id="id"
        :class="`peer h-12 w-full rounded-md border px-3 text-sm focus:outline-none focus:ring-0 focus:ring-primary ${
          error ? '!border-red-500' : '!border-border'
        }`"
      >
        <SelectValue />
      </SelectTrigger>
      <SelectContent>
        <SelectItem
          v-for="option in options"
          :key="option.value"
          :value="option.value"
          :disabled="option.disabled"
        >
          {{ option.label }}
        </SelectItem>
      </SelectContent>
    </Select>

    <Label
      v-if="label"
      :for="id"
      :class="`pointer-events-none absolute left-2  origin-[0] transform bg-background px-1 text-sm text-gray-500 transition-all duration-200 rounded-md ${
        shouldFloat
          ? 'top-0.5 -translate-y-1/2 scale-75 z-10'
          : 'top-4.5 -translate-y-1/2 scale-100 z-10'
      }`"
    >
      {{ label }}
    </Label>

    <p
      v-if="error"
      class="absolute top-8.5 pl-1 left-0 text-xs text-red-500"
    >
      {{ error }}
    </p>
  </div>
</template>