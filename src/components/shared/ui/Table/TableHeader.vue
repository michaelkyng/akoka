<script setup lang="ts">
import { ref, watch } from 'vue';
import { Search } from 'lucide-vue-next';
import { Input } from '@/components/ui/input';
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select/index.ts';

const searchQuery = ref('');
const selectedRole = ref('all');

const props = defineProps<{
  typeOptions?: { label: string; value: string }[];
  statusOptions: { label: string; value: string }[];
  selectedStatus?: string;
}>();

const selectedStatus = ref(props.selectedStatus || 'all');

// Watch for prop changes and update local state
watch(
  () => props.selectedStatus,
  newStatus => {
    if (newStatus !== undefined) {
      selectedStatus.value = newStatus;
    }
  },
  { immediate: true }
);

defineEmits<{
  search: [query: string];
  roleFilter: [role: string];
  statusFilter: [status: string];
}>();
</script>

<template>
  <div class="flex items-center justify-between mb-5">
    <!-- Search Input -->
    <div class="relative w-full max-w-180">
      <Search
        class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 w-4 h-4"
      />
      <Input
        v-model="searchQuery"
        placeholder="Search by name or email address"
        class="pl-10"
        @input="$emit('search', searchQuery)"
      />
    </div>

    <!-- Filter Dropdowns -->
    <div class="flex items-center gap-4">
      <!-- Role Filter -->
      <Select
        v-if="typeOptions"
        v-model="selectedRole"
        @update:model-value="$emit('roleFilter', selectedRole)"
      >
        <SelectTrigger class="w-fit">
          <SelectValue placeholder="Role" />
        </SelectTrigger>
        <SelectContent>
          <SelectItem
            v-for="role in typeOptions"
            :key="role.value"
            :value="role.value"
            >{{ role.label }}</SelectItem
          >
        </SelectContent>
      </Select>

      <!-- Status Filter -->
      <Select
        v-if="statusOptions"
        v-model="selectedStatus"
        @update:model-value="$emit('statusFilter', selectedStatus)"
      >
        <SelectTrigger class="w-32">
          <SelectValue placeholder="Status" />
        </SelectTrigger>
        <SelectContent>
          <SelectItem
            v-for="status in statusOptions"
            :key="status.value"
            :value="status.value"
            >{{ status.label }}</SelectItem
          >
        </SelectContent>
      </Select>
    </div>
  </div>
</template>
