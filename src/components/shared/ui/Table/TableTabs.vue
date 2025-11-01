<script setup lang="ts">
import { ref, type ComputedRef } from 'vue';
import { Tabs, TabsList, TabsTrigger } from '@/components/ui/tabs';
import { useRoute } from 'vue-router';
import { useRole } from '@/composables/useRoles';


const route = useRoute();
const {isClerk} = useRole()

const props = withDefaults(
  defineProps<{
    tabs: {
      label: string;
      value: string;
      count: ComputedRef;
    }[];
    activeTab: string;
    allUsersCount: number;
    adminsCount: number;
    clerksCount?: number;
    type?: 'users' | 'certificates';
  }>(),
  {
    type: 'certificates',
  }
);

const activeTab = ref(props.activeTab);

defineEmits<{
  tabChange: [tab: string];
}>();
</script>

<template>
  <Tabs
    v-model="activeTab"
    @update:model-value="$emit('tabChange', activeTab)"
    class="mb-6"
  >
    <TabsList class="flex gap-4 bg-transparent">
      <TabsTrigger
        v-if="type === 'certificates'"
        v-for="tab in tabs"
        :value="tab.value"
        class="!shadow-none !rounded-none"
        :class="tab.value === 'all' && !route.query.tab ? 'text-primary border-b-2 border-primary' : route.query.tab === tab.value ? 'text-primary border-b-2 border-primary' : ''"
      >
        {{
          isClerk && tab.value === 'corrections'
            ? 'My ' + tab.label
            : tab.label
        }}
        ({{ tab.count }})
      </TabsTrigger>
      <TabsTrigger
        v-else
        v-for="tab in tabs"
        :value="tab.value"
        class="!shadow-none !rounded-none data-[state=active]:text-primary data-[state=active]:border-b-2 data-[state=active]:border-primary"
      >
        {{ tab.label }}
        ({{ tab.count }})
      </TabsTrigger>
    </TabsList>
  </Tabs>
</template>
