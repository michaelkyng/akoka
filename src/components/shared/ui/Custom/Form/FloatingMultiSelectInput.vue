<script lang="ts" setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';

const props = defineProps({
  modelValue: {
    type: Array as () => string[],
    default: () => [],
  },
  options: {
    type: Array as () => { label: string; value: string }[],
    required: true,
    validator: (value: { label: string; value: string }[]) => {
      return value.every(option => 'label' in option && 'value' in option);
    },
  },
  label: {
    type: String,
    default: '',
  },
  placeholder: {
    type: String,
    default: 'Select options',
  },
  error: {
    type: String,
    default: '',
  },
});

const emit = defineEmits(['update:modelValue']);
const isOpen = ref(false);

const selectedOptions = computed(() => {
  return props.options.filter(option =>
    props.modelValue.includes(option.value)
  );
});

const filteredOptions = computed(() => {
  return props.options.filter(
    option => !props.modelValue.includes(option.value)
  );
});

const isSelected = (value: string) => {
  return props.modelValue.includes(value);
};

const toggleOption = (option: { value: string }) => {
  const newValue = [...props.modelValue];
  const index = newValue.indexOf(option.value);

  if (index === -1) {
    newValue.push(option.value);
  } else {
    newValue.splice(index, 1);
  }

  emit('update:modelValue', newValue);
};

const removeOption = (value: string) => {
  const newValue = props.modelValue.filter(item => item !== value);
  emit('update:modelValue', newValue);
};

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};

// Close dropdown when clicking outside
const handleClickOutside = (event: MouseEvent) => {
  if (!event.target || !(event.target as Element).closest('.relative')) {
    isOpen.value = false;
  }
};

onMounted(() => {
  document.addEventListener('click', handleClickOutside);
});

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside);
});
</script>

<template>
  <div class="w-full">
    <label v-if="label" class="mb-1 block text-sm font-medium text-gray-700">
      {{ label }}
    </label>
    <div class="relative">
      <div
        class="flex flex-wrap gap-1 rounded-md border border-gray-300 p-2"
        :class="{ 'border-red-500': error }"
        @click="toggleDropdown"
      >
        <span v-if="selectedOptions.length === 0" class="text-gray-500">
          {{ placeholder }}
        </span>
        <template v-else>
          <span
            v-for="option in selectedOptions"
            :key="option.value"
            class="inline-flex items-center rounded-full bg-blue-100 px-2.5 py-0.5 text-xs font-medium text-blue-800"
          >
            {{ option.label }}
            <button
              type="button"
              class="ml-1.5 inline-flex h-4 w-4 flex-shrink-0 items-center justify-center rounded-full text-blue-400 hover:bg-blue-200 hover:text-blue-500 focus:bg-blue-500 focus:text-white focus:outline-none"
              @click.stop="removeOption(option.value)"
            >
              <span class="sr-only">Remove {{ option.label }}</span>
              <svg
                class="h-2 w-2"
                stroke="currentColor"
                fill="none"
                viewBox="0 0 8 8"
              >
                <path
                  stroke-linecap="round"
                  stroke-width="1.5"
                  d="M1 1l6 6m0-6L1 7"
                />
              </svg>
            </button>
          </span>
        </template>
      </div>
      <div
        v-if="isOpen"
        class="absolute z-10 mt-1 max-h-60 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none sm:text-sm"
      >
        <div
          v-for="option in filteredOptions"
          :key="option.value"
          class="relative cursor-default select-none py-2 pl-3 pr-9 hover:bg-blue-100"
          :class="{
            'bg-blue-100': isSelected(option.value),
            'text-gray-900': !isSelected(option.value),
            'text-blue-900': isSelected(option.value),
          }"
          @click="toggleOption(option)"
        >
          <div class="flex items-center">
            <span
              class="ml-3 block truncate"
              :class="{ 'font-semibold': isSelected(option.value) }"
            >
              {{ option.label }}
            </span>
          </div>
          <span
            v-if="isSelected(option.value)"
            class="absolute inset-y-0 right-0 flex items-center pr-4 text-blue-600"
          >
            <svg
              class="h-5 w-5"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 20 20"
              fill="currentColor"
            >
              <path
                fill-rule="evenodd"
                d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                clip-rule="evenodd"
              />
            </svg>
          </span>
        </div>
      </div>
    </div>
    <p v-if="error" class="mt-1 text-sm text-red-600">
      {{ error }}
    </p>
  </div>
</template>
