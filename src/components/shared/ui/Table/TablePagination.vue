<script setup lang="ts">
import { computed } from 'vue';
import { ChevronLeft, ChevronRight, ChevronsLeft } from 'lucide-vue-next';
import { Button } from '@/components/ui/button';

const props = defineProps<{
  currentPage: number;
  totalPages: number;
}>();

defineEmits<{
  pageChange: [page: number];
}>();

// Calculate visible pages with better UX
const visiblePages = computed(() => {
  const pages = [];
  const current = props.currentPage;
  const total = props.totalPages;

  if (total <= 7) {
    // Show all pages if total is 7 or less
    for (let i = 1; i <= total; i++) {
      pages.push(i);
    }
  } else {
    // Show first page
    pages.push(1);

    if (current <= 4) {
      // Near beginning: show 1, 2, 3, 4, 5, ..., last
      for (let i = 2; i <= Math.min(5, total - 1); i++) {
        pages.push(i);
      }
      pages.push('...');
    } else if (current >= total - 3) {
      // Near end: show 1, ..., last-4, last-3, last-2, last-1, last
      for (let i = Math.max(2, total - 4); i <= total - 1; i++) {
        pages.push(i);
      }
    } else {
      // Middle: show 1, ..., current-1, current, current+1, ..., last
      pages.push('...');
      for (let i = current - 1; i <= current + 1; i++) {
        pages.push(i);
      }
      pages.push('...');
    }

    // Show last page if not already included
    if (total > 1) {
      pages.push(total);
    }
  }

  return pages;
});

const showGoToFirst = computed(() => {
  return props.currentPage > 10;
});
</script>

<template>
  <div class="flex items-center justify-between mt-6">
    <!-- Previous Button -->
    <Button
      variant="ghost"
      :disabled="currentPage <= 1"
      @click="$emit('pageChange', currentPage - 1)"
      class="flex items-center gap-2"
    >
      <ChevronLeft class="w-4 h-4" />
      <span>Previous</span>
    </Button>

    <!-- Page Numbers -->
    <div class="flex items-center gap-1">
      <!-- Go to First Page Button (when far from beginning) -->
      <Button
        v-if="showGoToFirst"
        variant="outline"
        class="w-10 h-10 text-xs"
        @click="$emit('pageChange', 1)"
        title="Go to first page"
      >
        <ChevronsLeft class="w-4 h-4" />
      </Button>

      <!-- Page Numbers -->
      <template v-for="page in visiblePages" :key="page">
        <Button
          v-if="typeof page === 'number'"
          :variant="page === currentPage ? 'default' : 'ghost'"
          :class="{
            'bg-primary hover:bg-primary/90 text-white': page === currentPage,
            'hover:bg-gray-100': page !== currentPage,
          }"
          class="w-10 h-10"
          @click="$emit('pageChange', page)"
        >
          {{ page }}
        </Button>

        <!-- Ellipsis -->
        <span v-else class="px-2 text-gray-500">...</span>
      </template>
    </div>

    <!-- Next Button -->
    <Button
      variant="ghost"
      :disabled="currentPage >= totalPages"
      @click="$emit('pageChange', currentPage + 1)"
      class="flex items-center gap-2"
    >
      <span>Next</span>
      <ChevronRight class="w-4 h-4" />
    </Button>
  </div>
</template>
