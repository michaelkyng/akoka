<!-- src/components/AppHeader.vue -->
<script setup lang="ts">
import { useRouter } from 'vue-router';
import { useAuthStore } from '@/stores/auth';
import UserMenu from './UserMenu.vue';
import NavbarNotification from './Notification.vue';

const router = useRouter();
const authStore = useAuthStore();

import { toast } from 'vue3-toastify';

async function onLogout() {
  try {
    await authStore.logout();
    toast.success('Logged out successfully!', { autoClose: 1000 });
    await new Promise(resolve => setTimeout(resolve, 1000));
    await router.push('/auth/login');
  } catch (error) {
    console.error('Logout failed:', error);
    toast.error('Failed to log out. Please try again.');
    await router.push('/auth/login');
  }
}
</script>

<template>
  <header
    class="fixed top-0 left-0 right-0 z-40 w-full border-b border-gray-200 bg-white"
  >
    <div class="container mx-auto flex items-center justify-between py-3 px-4">
      <div class="flex justify-end items-center gap-6 w-full">
        <NavbarNotification />

        <UserMenu
          v-if="authStore.user"
          :user="authStore.user"
          :logout="onLogout"
        />
      </div>
    </div>
  </header>
</template>
