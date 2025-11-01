<script lang="ts" setup>
import { ref, onMounted, onUnmounted } from 'vue';
import { Button } from '@/components/ui/button';
import { CircleAlert, CircleCheckBig } from 'lucide-vue-next';
import UploadInput from '@/components/shared/ui/Custom/Form/UploadInput.vue';
import { Separator } from '@/components/ui/separator';

// import api from '@/services/api'; // TODO: Uncomment when API is ready

import {
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
} from '@/components/ui/dialog';
import ScrollArea from '@/components/ui/scroll-area/ScrollArea.vue';

const props = defineProps<{
  uploadedFile?: File | null;
  isUploading?: boolean;
  uploadResults?: {
    successfulRowsCount: number;
    successfulRows: { rowNumber: number; data: any; status: string }[];
    failedRows: { rowNumber: number; data: any; status: string }[];
    failedRowsCount: number;
  } | null;
}>();

const formData = ref<File | null>(null);
const isMinimized = ref(false);

// Emits
const emit = defineEmits([
  'upload-file',
  'clear-file',
  'submit',
  'download-template',
]);

const clearFile = () => {
  emit('clear-file');
};

// Actions

const uploadFile = () => {
  if (!props.uploadedFile) return;
  emit('upload-file');
};

// Download failed rows as CSV
const downloadFailedRows = () => {
  if (!props.uploadResults?.failedRows.length) return;

  // Create CSV headers
  const headers = [
    'certificateId',
    'candidateFirstName',
    'candidateLastName',
    'issuanceDate',
  ];

  // Create CSV content
  const csvContent = [
    headers.join(','),
    ...props.uploadResults.failedRows.map(row =>
      headers.map(header => `"${row.data[header] || ''}"`).join(',')
    ),
  ].join('\n');

  // Create and download file
  const blob = new Blob([csvContent], { type: 'text/csv' });
  const url = window.URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = url;
  link.download = 'failed_rows.csv';
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
  window.URL.revokeObjectURL(url);
};

// Prevent default drag behaviors
const preventDefaults = (e: Event) => {
  e.preventDefault();
  e.stopPropagation();
};

function handleSubmit(event: Event) {
  event.preventDefault();
  emit('submit', formData.value);
}

onMounted(() => {
  ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
    document.addEventListener(eventName, preventDefaults, false);
  });
  isMinimized.value = false;
});

onUnmounted(() => {
  ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
    document.removeEventListener(eventName, preventDefaults, false);
  });
  isMinimized.value = false;
});

const handleMinimize = () => {
  isMinimized.value = true;
};

const handleMaximize = () => {
  isMinimized.value = false;
};
</script>

<template>
  <DialogContent
    class="sm:max-w-[600px] max-h-[90vh] overflow-hidden"
    can-minimize
    v-model:is-minimized="isMinimized"
    :handle-minimize="handleMinimize"
    :handle-maximize="handleMaximize"
  >
    <DialogHeader class="flex flex-col gap-2 justify-center py-4">
      <DialogTitle>
        <span class="text-2xl font-bold">Bulk Upload</span>
      </DialogTitle>
      <DialogDescription class="text-sm">
        Drag CSV files here to batch create certificates
      </DialogDescription>
    </DialogHeader>
    <ScrollArea class="max-h-[calc(95vh-200px)]">
      <form @submit.prevent="handleSubmit">
        <div class="overflow-y-auto max-h-[calc(90vh-200px)] space-y-6">
          <Transition name="fade" mode="out-in">
            <div
              v-if="uploadResults || !isMinimized"
              class="flex flex-col gap-6 w-full"
            >
              <div class="rounded-lg">
                <ul class="text-gray-500 list-none">
                  <li
                    class="relative pl-4 before:content-['•'] before:absolute before:left-0"
                  >
                    Click
                    <Button
                      variant="link"
                      class="!p-0 h-auto text-green-600 underline font-normal !text-base"
                      @click.prevent.stop="emit('download-template')"
                    >
                      here to download template
                    </Button>
                    option to get the correct format.
                  </li>
                  <li
                    class="relative pl-4 before:content-['•'] before:absolute before:left-0"
                  >
                    Make sure the table matches this templates.
                  </li>
                  <li
                    class="relative pl-4 before:content-['•'] before:absolute before:left-0"
                  >
                    You can update existing certificates or add new ones in the
                    same file.
                  </li>
                </ul>
              </div>
              <Separator />
            </div>
          </Transition>

          <UploadInput
            :uploaded-file="uploadedFile"
            :is-uploading="isUploading"
            accept=".csv,.xlsx,.xls"
            @clear-file="clearFile"
          />
        </div>

        <DialogFooter v-if="uploadedFile">
          <div v-if="!uploadResults" class="flex gap-2 w-full">
            <Button
              v-if="!isUploading"
              variant="outline"
              @click.prevent.stop="clearFile"
              class="flex-1"
            >
              Clear File
            </Button>
            <Button
              @click.prevent.stop="uploadFile"
              :disabled="isUploading"
              class="flex-1"
            >
              <span v-if="isUploading">Uploading...</span>
              <span v-else>Upload File</span>
            </Button>
          </div>

          <!-- Upload Results -->
          <div v-if="uploadResults" class="mt-6 space-y-4 w-full">
            <!-- Failed Rows List -->
            <div
              v-if="uploadResults.failedRowsCount > 0"
              class="bg-white border rounded-lg p-4"
            >
              <h4 class="font-medium text-gray-900 mb-3">Failed Rows</h4>
              <div
                class="space-y-2 max-h-52 overflow-y-scroll [scrollbar-width:thin] [scrollbar-color:var(--color-primary)_#e5e7eb] [&::-webkit-scrollbar]:w-2 [&::-webkit-scrollbar-track]:bg-gray-200 [&::-webkit-scrollbar-thumb]:bg-primary [&::-webkit-scrollbar-thumb]:rounded-full"
              >
                <div
                  v-for="row in uploadResults.failedRows"
                  :key="row.rowNumber"
                  class="flex items-center justify-between py-2 px-3 bg-red-50 rounded"
                >
                  <div class="flex flex-col">
                    <span class="text-sm font-medium"
                      >Row {{ row.rowNumber }}</span
                    >
                  </div>
                  <div class="flex items-center gap-2">
                    <div
                      class="size-3 bg-red-500 rounded-full flex items-center justify-center"
                    >
                      <CircleAlert class="size-4 text-white" />
                    </div>
                    <span class="text-sm text-red-600 font-medium">{{
                      row.status
                    }}</span>
                  </div>
                </div>
              </div>
            </div>

            <!-- Summary Results -->
            <div class="space-y-3">
              <!-- Successful Rows -->
              <div
                class="flex items-center justify-between p-3 bg-primary/5 rounded-lg"
              >
                <div class="flex items-center gap-2">
                  <div
                    class="size-fit bg-primary rounded-full flex items-center justify-center"
                  >
                    <CircleCheckBig class="size-4 text-white" />
                  </div>
                  <span class="text-sm text-primary">
                    {{ uploadResults.successfulRowsCount.toLocaleString() }}
                    rows successful
                  </span>
                </div>
              </div>

              <!-- Failed Rows -->
              <div
                v-if="uploadResults.failedRowsCount > 0"
                class="flex items-center justify-between p-3 bg-red-50 rounded-lg"
              >
                <div class="flex items-center gap-2">
                  <div
                    class="size-fit bg-red-500 rounded-full flex items-center justify-center"
                  >
                    <CircleAlert class="size-4 text-white" />
                  </div>
                  <span class="text-sm text-red-800">
                    {{ uploadResults.failedRowsCount.toLocaleString() }}
                    rows failed
                  </span>
                </div>
                <Button
                  variant="link"
                  class="text-orange-600 hover:text-orange-700 p-0 h-auto text-sm"
                  @click.prevent.stop="downloadFailedRows"
                >
                  Download failed rows
                </Button>
              </div>
            </div>

            <!-- Action Button -->
            <div class="pt-4">
              <Button
                @click.prevent.stop="emit('submit')"
                class="w-full py-3 text-base font-bold"
                :disabled="uploadResults.successfulRowsCount === 0"
              >
                Commit
                {{ uploadResults.successfulRowsCount.toLocaleString() }}
                successful rows to Blockchain
              </Button>
            </div>
          </div>
        </DialogFooter>
      </form>
    </ScrollArea>
  </DialogContent>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease-in-out;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  height: 0;
}

.fade-enter-to,
.fade-leave-from {
  opacity: 1;
  height: 90px;
}
</style>
