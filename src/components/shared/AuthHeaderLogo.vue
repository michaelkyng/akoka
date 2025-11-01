<template>
  <div class="auth-header-logo flex items-center justify-center">
    <div class="logo-container relative">
      <!-- Logo Icon -->
      <div
        class="w-12 h-12 bg-primary rounded-lg flex items-center justify-center shadow-lg"
      >
        <svg
          class="w-8 h-8 text-white"
          fill="none"
          stroke="currentColor"
          viewBox="0 0 24 24"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
          />
        </svg>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
// No props needed for this component
</script>

<style scoped>
.auth-header-logo {
  min-height: 60px;
}

.logo-container {
  display: flex;
  align-items: center;
}
</style>
