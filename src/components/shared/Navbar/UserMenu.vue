<script setup lang="ts">
import { ref, computed } from 'vue';
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu';
import { Button } from '@/components/ui/button';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import {
  ChevronDown,
  ChevronUp,
  LogOut,
  Settings2,
  User as UserIcon,
} from 'lucide-vue-next';
import type { IUser } from '@/types/user';

interface Props {
  user: IUser | null;
  logout: () => Promise<void>;
}

const props = defineProps<Props>();

const isOpen = ref(false);

const handleLogout = () => {
  props.logout();
};

const userInitial = computed(() => {
  if (!props.user) return 'U';
  return (
    props.user?.firstName?.split(' ')[0]?.charAt(0) ||
    props.user?.email?.charAt(0) ||
    'U'
  ).toUpperCase();
});
</script>

<template>
  <DropdownMenu v-model:open="isOpen">
    <DropdownMenuTrigger as-child>
      <Button
        variant="ghost"
        class="flex items-center gap-2 h-9 w-auto p-0 border-0 outline-0 hover:bg-secondary hover:text-black"
      >
        <Avatar class="h-8 w-8">
          <AvatarImage :src="`https://api.dicebear.com/7.x/initials/svg?seed=${user?.first_name}`" :alt="user?.name" />
          <AvatarFallback>{{ userInitial }}</AvatarFallback>
        </Avatar>

        <ChevronUp v-if="isOpen" class="w-5 h-5" />
        <ChevronDown v-else class="w-5 h-5" />
      </Button>
    </DropdownMenuTrigger>
    <DropdownMenuContent class="w-56" align="end">
      <DropdownMenuLabel class="font-normal">
        <div class="flex flex-col space-y-1">
          <p class="text-sm font-medium leading-none">
            {{ user?.first_name || 'User' }}
          </p>
          <p class="text-xs leading-none text-muted-foreground">
            {{ user?.email }}
          </p>
        </div>
      </DropdownMenuLabel>
      <DropdownMenuSeparator />
      <DropdownMenuItem as-child>
        <router-link to="/profile" class="cursor-pointer flex items-center">
          <UserIcon class="mr-2 h-4 w-4 hover:text-white" />
          <span>Profile</span>
        </router-link>
      </DropdownMenuItem>
      <DropdownMenuItem as-child>
        <router-link to="/settings" class="cursor-pointer">
          <Settings2 class="mr-2 h-4 w-4 hover:text-white" />
          <span>Settings</span>
        </router-link>
      </DropdownMenuItem>
      <DropdownMenuSeparator />
      <DropdownMenuItem
        @click="handleLogout"
        class="text-red-500 focus:text-red-50"
      >
        <LogOut class="mr-2 h-4 w-4 hover:text-white" />
        <span>Log out</span>
      </DropdownMenuItem>
    </DropdownMenuContent>
  </DropdownMenu>
</template>
