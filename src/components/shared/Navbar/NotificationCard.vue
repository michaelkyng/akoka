<script setup lang="ts">
import type { Notification } from '@/types/notification';

defineProps<{
  notification: Notification;
}>();

</script>

<template>
  <router-link
    :to="notification.href"
    class="block p-3 transition-colors rounded-md cursor-pointer hover:bg-muted/50"
  >
    <div class="flex items-start gap-3">
      <div
        v-if="!notification.isRead"
        :class="`w-2 h-2 bg-primary rounded-full mt-2 flex-shrink-0`"
        aria-label="Unread notification"
      />
      <div
        :class="`flex-1 space-y-1 min-w-0 ${notification.isRead ? 'pl-5' : ''}`"
      >
        <p
          :class="`text-sm font-medium truncate ${
            notification.isRead ? 'text-muted-foreground' : 'text-foreground'
          }`"
        >
          {{ notification.title }}
        </p>
        <p class="text-xs truncate text-muted-foreground">
          {{ notification.message }}
        </p>
        <p class="text-xs text-muted-foreground/80">
          {{ notification.timestamp }}
        </p>
      </div>
    </div>
  </router-link>
</template>
