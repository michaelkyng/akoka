<script setup lang="ts">
defineProps<{
  variant?: 'outline' | 'filled';
  animated?: boolean;
}>();
</script>

<template>
  <transition :name="animated ? 'icon' : ''" mode="out-in">
    <svg
      v-if="variant === 'outline'"
      key="outline"
      width="20"
      height="20"
      viewBox="0 0 20 20"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path
        d="M12.4999 1.66663H4.99992C4.55789 1.66663 4.13397 1.84222 3.82141 2.15478C3.50885 2.46734 3.33325 2.89127 3.33325 3.33329V16.6666C3.33325 17.1087 3.50885 17.5326 3.82141 17.8451C4.13397 18.1577 4.55789 18.3333 4.99992 18.3333H14.9999C15.4419 18.3333 15.8659 18.1577 16.1784 17.8451C16.491 17.5326 16.6666 17.1087 16.6666 16.6666V5.83329L12.4999 1.66663Z"
        stroke="currentColor"
        stroke-width="1.25"
        stroke-linecap="round"
        stroke-linejoin="round"
      />
      <path
        d="M11.6667 1.66663V4.99996C11.6667 5.44199 11.8423 5.86591 12.1549 6.17847C12.4675 6.49103 12.8914 6.66663 13.3334 6.66663H16.6667"
        stroke="currentColor"
        stroke-width="1.25"
        stroke-linecap="round"
        stroke-linejoin="round"
      />
    </svg>

    <svg
      v-else
      key="filled"
      width="20"
      height="20"
      viewBox="0 0 20 20"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path
        d="M11.042 5C11.042 5.60762 11.2833 6.19038 11.7129 6.62012C12.1426 7.04981 12.7263 7.29199 13.334 7.29199H17.291V16.667C17.2909 17.2746 17.0497 17.8574 16.6201 18.2871C16.1905 18.7166 15.6075 18.9589 15 18.959H5C4.39233 18.9589 3.80861 18.7168 3.37891 18.2871C2.94943 17.8575 2.7081 17.2745 2.70801 16.667V3.33398C2.70801 2.72634 2.94933 2.14263 3.37891 1.71289C3.80862 1.28318 4.3923 1.04206 5 1.04199H11.042V5ZM12.6221 1.05371C12.7421 1.07767 12.8535 1.13684 12.9414 1.22461L17.1084 5.3916C17.2256 5.50881 17.291 5.66825 17.291 5.83398V6.04199H13.334C13.0578 6.04199 12.792 5.9316 12.5967 5.73633C12.4015 5.54101 12.292 5.2761 12.292 5V1.04199H12.5L12.6221 1.05371Z"
        fill="currentColor"
      />
    </svg>
  </transition>
</template>

<style scoped>
.icon-enter-active,
.icon-leave-active {
  transition: all 0.2s ease-in-out;
}

.icon-enter-from {
  opacity: 0;
  transform: translateY(100%);
}

.icon-leave-to {
  opacity: 0;
  transform: translateY(-100%);
}
</style>
