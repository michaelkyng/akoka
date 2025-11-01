<script lang="ts" setup>
defineOptions({
  name: 'CustomSidebar',
});

import { useRouter } from 'vue-router';
import {
  SidebarContent,
  SidebarFooter,
  SidebarGroup,
  SidebarGroupContent,
  SidebarHeader,
  SidebarMenu,
  SidebarMenuButton,
  SidebarMenuItem,
} from '@/components/ui/sidebar';
import {
  Tooltip,
  TooltipContent,
  TooltipTrigger,
} from '@/components/ui/tooltip';
import Home from '@/components/shared/icons/Home.vue';
import Userplus from '@/components/shared/icons/User-plus.vue';
import InfoSquare from '@/components/shared/icons/Info-square.vue';
import Settings from '@/components/shared/icons/Settings.vue';
import Logout from '@/components/shared/icons/Logout.vue';
import File from '@/components/shared/icons/File.vue';
import { useSidebar } from '@/components/ui/sidebar';
import type { ComponentCustomElementInterface } from 'vue';

const router = useRouter();


const sideBarItems = [
  {
    label: 'Dashboard',
    icon: Home,
    path: '/',
    // roles: ['admin', 'operator'],
  },
  {
    label: 'Certificate',
    icon: File,
    path: '/certificates',
    // roles: ['admin', 'operator'],
  },
  {
    label: 'Operators',
    icon: Userplus,
    path: '/users',
    // roles: ['admin'],
  },
  {
    label: 'Logs',
    icon: InfoSquare,
    path: '/logs',
    // roles: ['admin', 'operator'],
  },
  {
    label: 'Settings',
    icon: Settings,
    path: '/settings',
    // roles: ['admin', 'operator'],
  },
] as {
  label : string,
  icon : ComponentCustomElementInterface,
  path : string,
  // roles : UserRole[]
}[];



const isActiveRoute = (path: string) => {
  const currentPath = router.currentRoute.value.path;

  if (path === '/') {
    return currentPath === '/';
  }

  return currentPath.startsWith(path);
};

const { open: sidebarOpen } = useSidebar();
</script>

<template>
  <div class="sidebar h-full">
    <div class="flex flex-col justify-between h-full">
      <SidebarHeader class="p-4 border-b">
        <h2 class="text-lg font-semibold">CertifyAI</h2>
      </SidebarHeader>
      <SidebarContent class="py-4">
        <SidebarGroup>
          <SidebarGroupContent>
            <SidebarMenu>
              <!-- Dashboard -->
              <template v-for="item in sideBarItems" :key="item.path">
                <SidebarMenuItem>
                  <Tooltip class="sidebar-tooltip">
                    <TooltipTrigger as-child>
                      <SidebarMenuButton
                        :is-active="isActiveRoute(item.path)"
                        exact-active-class="!bg-primary/5 !text-primary"
                        class="py-3 h-fit transition-all duration-200 ease-in-out"
                        as-child
                      >
                        <router-link
                          :to="item.path"
                          class="flex items-center gap-3 w-full"
                        >
                          <component
                            :is="item.icon"
                            class="w-5 h-5"
                            :variant="
                              isActiveRoute(item.path) ? 'filled' : 'outline'
                            "
                            :animated="isActiveRoute(item.path)"
                          />
                          <span class="font-medium">{{ item.label }}</span>
                        </router-link>
                      </SidebarMenuButton>
                    </TooltipTrigger>
                    <TooltipContent
                      v-if="!sidebarOpen"
                      side="right"
                      class="fill-primary"
                    >
                      <p>{{ item.label }}</p>
                    </TooltipContent>
                  </Tooltip>
                </SidebarMenuItem>
              </template>
            </SidebarMenu>
          </SidebarGroupContent>
        </SidebarGroup>
      </SidebarContent>
      <SidebarFooter class="py-4 border-t">
        <SidebarMenu>
          <SidebarMenuItem>
            <Tooltip class="sidebar-tooltip">
              <TooltipTrigger as-child>
                <SidebarMenuButton
                  class="text-red-500 hover:text-red-700 hover:bg-red-50 w-full justify-start cursor-pointer"
                >
                  <Logout class="w-5 h-5" />
                  <span class="font-medium">Log Out</span>
                </SidebarMenuButton>
              </TooltipTrigger>
              <TooltipContent
                v-if="!sidebarOpen"
                side="right"
                color="red-500"
                class="text-white fill-red-500"
              >
                <p>Log Out</p>
              </TooltipContent>
            </Tooltip>
          </SidebarMenuItem>
        </SidebarMenu>
      </SidebarFooter>
    </div>
  </div>
</template>
