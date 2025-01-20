<script setup>
import  HeaderSection from '@/Layout/HeaderSection.vue'
import FooterSection from '@/Layout/FooterSection.vue'
import { RouterView } from 'vue-router';

</script>

<template>
  <div>
    <HeaderSection />
    <main>
      <RouterView />
    </main>
    <FooterSection />
  </div>
</template>

<style>
</style>
