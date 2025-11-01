<script setup lang="ts">
import { Button } from '@/components/ui/button';
import { computed, type HTMLAttributes, type PropType } from 'vue';
import {
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
} from '@/components/ui/dialog';
import type { FormDialogConfig } from '@/types/component';
import ScrollArea from '@/components/ui/scroll-area/ScrollArea.vue';

const props = defineProps({
  isSubmitting: {
    type: Boolean,
    default: false,
  },
  config: {
    type: Object as () => FormDialogConfig,
    required: false,
  },
  isFormValid: {
    type: Boolean,
    default: true,
  },
  size: {
    type: String as () => 'sm' | 'md' | 'lg',
    default: 'sm',
  },
  class: {
    type: String as PropType<HTMLAttributes['class']>,
    default: '',
  },
});

const sizeClasses = computed(() => {
  switch (props.size) {
    case 'sm':
      return 'sm:max-w-[600px]';
    case 'md':
      return 'sm:max-w-[850px]';
    case 'lg':
      return 'sm:max-w-[1004px]';
  }
});

const emit = defineEmits(['submit']);

function handleSubmit(event: Event) {
  event.preventDefault();
  emit('submit');
}

// Get title color based on variant
const getTitleClasses = () => {
  const baseClasses = 'text-2xl font-bold';
  switch (props.config?.variant) {
    case 'destructive':
      return `${baseClasses}`;
    case 'success':
      return `${baseClasses}`;
    default:
      return baseClasses;
  }
};
</script>

<template>
  <DialogContent class="max-h-[95vh]" :class="[sizeClasses,props.class]">
    <DialogHeader
      class="flex flex-col gap-2 justify-center"
      :class="config?.titleCenter ? 'items-center' : 'items-start'"
    >
      <slot name="header">
        <DialogTitle :class="getTitleClasses()">
          {{ config?.title }}
        </DialogTitle>
        <DialogDescription
          v-if="config?.description"
          class="text-sm"
          :class="config?.titleCenter ? 'text-center' : 'text-start'"
        >
          {{ config?.description }}
        </DialogDescription>
      </slot>
    </DialogHeader>

    <ScrollArea class="max-h-[calc(95vh-200px)]">
      <form @submit.prevent="handleSubmit" class="grid gap-5 py-4">
        <!-- Slot for form inputs -->
        <slot name="form-content" />
      </form>

      <DialogFooter>
        <!-- Slot for custom footer content (optional) -->
        <slot name="footer-content">
          <Button
            type="submit"
            variant="default"
            class="w-full !py-2 !font-bold !text-base"
            :disabled="isSubmitting || !isFormValid"
            @click="handleSubmit"
          >
            {{
              isSubmitting
                ? config?.loadingText || 'Processing...'
                : config?.submitText
            }}
          </Button>
        </slot>
      </DialogFooter>
    </ScrollArea>
  </DialogContent>
</template>

<style scoped></style>
