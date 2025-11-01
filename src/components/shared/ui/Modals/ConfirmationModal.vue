<script lang="ts" setup>
import { Button } from '@/components/ui/button';
import {
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
  Dialog,
} from '@/components/ui/dialog';
import ConfirmationContent from '../Custom/Dialog/ConfirmationContent.vue';
import type { ConfirmationConfig } from '@/types/component';
import ScrollArea from '@/components/ui/scroll-area/ScrollArea.vue';
import { computed } from 'vue';

const props = defineProps({
  isSubmitting: {
    type: Boolean,
    default: false,
  },
  showConfirmationModal: {
    type: Boolean,
    required: true,
  },
  config: {
    type: Object as () => ConfirmationConfig,
    default: () => ({
      title: '',
      confirmText: 'OK',
      loadingText: 'Processing...',
      variant: 'default',
      showMetadata: false,
      titleCenter: true,
    }),
  },
  size: {
    type: String as () => 'sm' | 'md' | 'lg',
    default: 'sm',
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

const emit = defineEmits(['submit', 'update:showConfirmationModal']);

function handleSubmit(event: Event) {
  event.preventDefault();
  emit('submit');
}

const getTitleClasses = () => {
  const baseClasses = 'text-2xl font-bold';
  switch (props.config.variant) {
    case 'destructive':
      return `${baseClasses}`;
    case 'default':
      return `${baseClasses}`;
    default:
      return baseClasses;
  }
};
</script>

<template>
  <Dialog
    :open="showConfirmationModal"
    @update:open="emit('update:showConfirmationModal', $event)"
    class="overflow-hidden"
  >
    <ConfirmationContent class="max-h-[90vh]" :class="sizeClasses">
      <DialogHeader
        class="flex flex-col gap-2 py-4"
        :class="config.titleCenter ? 'justify-center items-center' : ''"
      >
        <DialogTitle
          :class="getTitleClasses() + (config.titleCenter ? 'text-center' : '')"
        >
          {{ config.title }}
        </DialogTitle>

        <DialogDescription
          v-if="config.showMetadata && config.metadataText"
          class="text-sm"
          :class="config.titleCenter ? 'text-center' : ''"
        >
          {{ config.metadataText }}
        </DialogDescription>
      </DialogHeader>

      <ScrollArea class="max-h-[calc(95vh-200px)]" :class="config.titleCenter ? 'px-10' : ''">
        <div class="flex flex-col gap-2">
          <slot name="custom-content">
            <p v-if="config.message" class="text-sm text-center">
              {{ config.message }}
            </p>

            <p
              v-if="config.description"
              class="text-sm text-center text-muted-foreground"
            >
              {{ config.description }}
            </p>
          </slot>
        </div>

        <DialogFooter>
          <slot name="footer-content">
            <Button
              type="submit"
              :variant="
                config.variant === 'success'
                  ? 'default'
                  : config.variant || 'default'
              "
              :disabled="isSubmitting"
              @click="handleSubmit"
              class="w-full !py-3 !font-bold !text-base mt-5"
            >
              {{
                isSubmitting
                  ? config.loadingText || 'Processing...'
                  : config.confirmText
              }}
            </Button>
          </slot>
        </DialogFooter>
      </ScrollArea>
    </ConfirmationContent>
  </Dialog>
</template>

<style scoped></style>
