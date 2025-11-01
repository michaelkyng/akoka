<script lang="ts" setup>
import Progress from '@/components/ui/progress/Progress.vue';
import FileIcon from '@/components/shared/icons/File.vue';
import BulkUpload from '@/components/shared/icons/BulkUpload.vue';
import { X } from 'lucide-vue-next';
import { Button } from '@/components/ui/button';
import { ref, inject, type Ref } from 'vue';

const fileProgress = inject('fileProgress') as Ref<number>;
const handleFileSelected = inject('handleFileSelected') as (file: File) => void;

defineProps<{
  uploadedFile?: File | null;
  isUploading?: boolean;
  label?: string;
  placeholder?: string;
  error?: string;
  id?: string;
  accept?: string;
  disabled?: boolean;
}>();

const emit = defineEmits(['clear-file']);

// Reactive state
const isDragOver = ref(false);
const fileInput = ref<HTMLInputElement | null>(null);

// Drag and drop handlers
const handleDragOver = (e: DragEvent) => {
  e.preventDefault();
  isDragOver.value = true;
};

const handleDragEnter = (e: DragEvent) => {
  e.preventDefault();
  isDragOver.value = true;
};

const handleDragLeave = (e: DragEvent) => {
  e.preventDefault();
  if (!(e.currentTarget as Element)?.contains(e.relatedTarget as Node)) {
    isDragOver.value = false;
  }
};

const handleDrop = (e: DragEvent) => {
  e.preventDefault();
  isDragOver.value = false;

  const files = Array.from(e.dataTransfer?.files || []);
  if (files.length > 0) {
    handleFileSelected(files[0]);
  }
};

// File handling
const browseFiles = () => {
  fileInput.value?.click();
};

const handleFileSelect = (e: Event) => {
  const target = e.target as HTMLInputElement;
  const files = Array.from(target.files || []);
  if (files.length > 0) {
    handleFileSelected(files[0]);
  }
  target.value = '';
};

const formatFileSize = (bytes: number) => {
  if (bytes === 0) return '0 Bytes';
  const k = 1024;
  const sizes = ['Bytes', 'KB', 'MB', 'GB'];
  const i = Math.floor(Math.log(bytes) / Math.log(k));
  return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
};

const removeFile = () => {
  emit('clear-file');
};
</script>
<template>
  <div>
    <div class="flex flex-col">
      <!-- Upload Area -->
      <div
        v-if="!uploadedFile"
        class="border-2 border-dashed border-gray-300 rounded-lg p-12 text-center bg-gray-50 transition-colors cursor-pointer"
        :class="{
          'border-blue-400 bg-blue-50': isDragOver,
          '!border-red-500': error,
        }"
        @click="browseFiles"
        @drop="handleDrop"
        @dragover="handleDragOver"
        @dragenter="handleDragEnter"
        @dragleave="handleDragLeave"
      >
        <div class="flex flex-col gap-2 items-center justify-center">
          <BulkUpload class="size-6 text-gray-400" />
          <div class="flex flex-col items-center gap-1">
            <p class="text-gray-600">Drag files here to upload</p>
            <p class="text-xs text-primary cursor-pointer" @click="browseFiles">
              or browse for files
            </p>
          </div>
        </div>
        <input
          ref="fileInput"
          type="file"
          :accept="accept ? accept : 'image/*'"
          class="hidden"
          @change="handleFileSelect"
          :disabled="disabled ? disabled : false"
        />
      </div>
      <span v-if="error" class="mt-2 text-red-500 text-sm">{{ error }}</span>
      <!-- File Display -->
      <div v-if="uploadedFile" class="mt-6">
        <div
          class="flex flex-col gap-3 w-full h-fit items-center justify-between p-3 bg-gray-50 rounded-lg"
        >
          <div class="flex w-full items-center justify-between">
            <div class="flex items-center gap-3">
              <FileIcon class="size-4 text-gray-500" />
              <div class="flex flex-col">
                <span class="text-sm font-medium">{{ uploadedFile.name }}</span>
                <span class="text-xs text-gray-500">{{
                  formatFileSize(uploadedFile.size)
                }}</span>
              </div>
            </div>
            <Button
              variant="ghost"
              size="sm"
              @click="removeFile"
              class="text-red-500 hover:text-red-700"
            >
              <X class="h-4 w-4" />
            </Button>
          </div>
          <div v-if="isUploading" class="rounded-lg w-full">
            <div class="flex items-center justify-end mb-1">
              <span class="text-xs text-gray-500"
                >{{ Math.round(fileProgress || 0) }}%</span
              >
            </div>
            <Progress :model-value="fileProgress || 0" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
