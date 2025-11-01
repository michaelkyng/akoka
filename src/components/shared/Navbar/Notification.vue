<script setup lang="ts">
import { computed } from 'vue';
import {
  DropdownMenu,
  DropdownMenuTrigger,
  DropdownMenuContent,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuItem,
} from '@/components/ui/dropdown-menu';
import { Button } from '@/components/ui/button';
import NotificationIcon from '@/components/shared/icons/NotificationIcon.vue';
import NotificationCard from './NotificationCard.vue';
import { mockNotifications } from '@/data';

const unreadCount = computed(() => {
  return mockNotifications.filter(n => !n.isRead).length;
});
</script>

<template>
  <DropdownMenu>
    <DropdownMenuTrigger as-child>
      <Button
        variant="ghost"
        size="icon"
        class="relative w-9 h-9 border border-border hover:bg-secondary hover:text-secondary"
      >
        <NotificationIcon :size="20" />
        <span
          v-if="unreadCount > 0"
          class="absolute flex items-center justify-center w-2 h-2 top-2 left-[18px] text-xs bg-[#00A2D4] rounded-full"
        />
      </Button>
    </DropdownMenuTrigger>
    <DropdownMenuContent class="w-80" align="end">
      <DropdownMenuLabel>
        <div class="flex items-center justify-between">
          <span>Notifications</span>
          <span v-if="unreadCount > 0" class="text-xs text-muted-foreground">
            {{ unreadCount }} new
          </span>
        </div>
      </DropdownMenuLabel>
      <DropdownMenuSeparator />
      <div
        class="p-1 overflow-y-auto max-h-96 scrollbar-thin scrollbar-thumb-primary scrollbar-track-gray-100 hover:scrollbar-thumb-primary/80"
      >
        <template v-if="mockNotifications.length > 0">
          <NotificationCard
            v-for="notification in mockNotifications"
            :key="notification.id"
            :notification="notification"
          />
        </template>
        <div v-else class="py-8 text-sm text-center text-muted-foreground">
          You have no new notifications.
        </div>
      </div>
      <DropdownMenuSeparator />
      <DropdownMenuItem as-child>
        <router-link
          to="/notifications"
          class="justify-center cursor-pointer text-primary hover:text-primary/90"
        >
          View all notifications
        </router-link>
      </DropdownMenuItem>
    </DropdownMenuContent>
  </DropdownMenu>
</template>
