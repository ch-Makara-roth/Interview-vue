<script setup>
import HeaderSection from '@/Layout/HeaderSection.vue'
import FooterSection from '@/Layout/FooterSection.vue'
import { RouterView } from 'vue-router'
</script>

<template>
  <div class="min-h-screen flex flex-col">
    <!-- Header -->
    <HeaderSection />

    <!-- Main Content -->
    <main class="flex-grow container mx-auto px-4 py-8">
      <RouterView />
    </main>

    <!-- Footer -->
    <FooterSection />
  </div>
</template>

<style>
/* Add global styles here if needed */
</style>
