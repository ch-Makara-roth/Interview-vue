<script setup>
import { RouterLink } from 'vue-router'

</script>
<template>
      <header>
      <div class="bg-gray-200 py-4">
        <div class="container mx-auto flex justify-between items-center px-4">
          <div>
            <h1 class="text-2xl font-bold">ACM - Interview </h1>
          </div>
          <nav class="space-x-4">
            <RouterLink to="/" class="text-lg hover:text-blue-600">Home</RouterLink>
            <RouterLink to="/about" class="text-lg hover:text-blue-600">About</RouterLink>
          </nav>
        </div>
      </div>
      </header>
</template>

<style scoped>
.router-link-active {
  font-weight: bold;
  color: #2563eb; /* blue-600 */
}
</style>
