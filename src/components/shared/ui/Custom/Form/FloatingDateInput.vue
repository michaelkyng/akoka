<script lang="ts" setup>
import { computed, ref } from 'vue';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Calendar } from '@/components/ui/calendar';
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from '@/components/ui/popover';
import { CalendarIcon } from 'lucide-vue-next';
import { CalendarDate } from '@internationalized/date';
import type { HTMLAttributes, PropType } from 'vue';

const props = defineProps({
  id: {
    type: String,
    required: true,
  },
  modelValue: {
    type: String,
    default: '',
  },
  label: {
    type: String,
    required: true,
  },
  placeholder: {
    type: String,
    default: 'dd/mm/yyyy',
  },
  class: {
    type: String as PropType<HTMLAttributes['class']>,
    default: '',
  },
  error: {
    type: [String, Object],
    default: undefined,
  },
  floating: {
    type: Boolean,
    default: true,
  },
  showCalendar: {
    type: Boolean,
    default: true,
  },
  background: {
    type: String,
    default: 'bg-background',
  },
});

const emit = defineEmits(['update:modelValue']);

const open = ref(false);

const model = computed({
  get: () => props.modelValue,
  set: (value: string) => emit('update:modelValue', value),
});

// Format date from Date object to dd/mm/yyyy
const formatDate = (date: Date) => {
  return date.toLocaleDateString('en-GB', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
  });
};

// Parse UK date format (dd/mm/yyyy) to CalendarDate
const parseToCalendarDate = (dateString: string) => {
  if (!dateString) return undefined;
  const parts = dateString.split('/');
  if (parts.length === 3) {
    const day = parseInt(parts[0]);
    const month = parseInt(parts[1]);
    const year = parseInt(parts[2]);

    // Validate the date parts
    if (
      !isNaN(day) &&
      !isNaN(month) &&
      !isNaN(year) &&
      day >= 1 &&
      day <= 31 &&
      month >= 1 &&
      month <= 12 &&
      year >= 1900
    ) {
      return new CalendarDate(year, month, day);
    }
  }
  return undefined;
};

// Handle manual input change
const handleInput = (event: Event) => {
  const target = event.target as HTMLInputElement;
  let value = target.value;

  // Auto-format as user types: add slashes after day and month
  value = value.replace(/\D/g, ''); // Remove non-digits

  if (value.length >= 2) {
    value = value.slice(0, 2) + '/' + value.slice(2);
  }
  if (value.length >= 5) {
    value = value.slice(0, 5) + '/' + value.slice(5, 9);
  }

  // Update the model value
  emit('update:modelValue', value);
};

// Handle date selection from calendar
const handleDateSelect = (date: any) => {
  if (date) {
    const selectedDate = new Date(date.year, date.month - 1, date.day);
    model.value = formatDate(selectedDate);
  } else {
    model.value = '';
  }
  open.value = false;
};
</script>

<template>
  <div class="relative h-full max-h-12 flex-1">
    <Popover v-model:open="open">
      <PopoverTrigger as-child>
        <div class="relative">
          <Input
            :id="id"
            v-model="model"
            type="text"
            :placeholder="floating ? placeholder : ' '"
            :error="error"
            class="peer pr-10"
            :class="[{ 'border-red-500': error }, background, props.class]"
            maxlength="10"
            @input="handleInput"
          />
          <Label
            :for="id"
            class="peer-focus:secondary peer-focus:dark:secondary absolute start-2 top-2 z-10 origin-[0] -translate-y-4 scale-75 transform bg-background px-2 text-sm text-gray-500 duration-300 peer-placeholder-shown:top-1/2 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:scale-100 peer-focus:top-2 peer-focus:-translate-y-4 peer-focus:scale-75 peer-focus:px-2 dark:bg-background rtl:peer-focus:left-auto rtl:peer-focus:translate-x-1/4 cursor-text rounded-md"
            :class="background"
          >
            {{ label }}
          </Label>
          <button
            v-if="showCalendar"
            type="button"
            @click.stop="open = !open"
            class="absolute -right-6 top-1/2 -translate-y-1/2 text-gray-500 hover:text-gray-700 focus:outline-none focus:text-gray-700 transition-colors duration-200"
          >
            <CalendarIcon class="size-4" />
          </button>
        </div>
      </PopoverTrigger>
      <PopoverContent class="w-auto p-0" align="start">
        <Calendar
          :default-value="parseToCalendarDate(model)"
          @update:model-value="handleDateSelect"
        />
      </PopoverContent>
    </Popover>
    <p v-if="error" class="absolute top-8.5 pl-1 left-0 text-xs text-red-500">
      {{ error }}
    </p>
  </div>
</template>
