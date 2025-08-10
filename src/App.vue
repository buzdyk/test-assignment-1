<script setup>
import { ref } from 'vue'
import ServiceFrequency from './components/ServiceFrequency.vue'

const scheduleConfig = ref('')
</script>

<template>
  <div class="min-h-screen bg-background py-8">
    <div class="container mx-auto px-4">
      <h1 class="text-3xl font-bold text-center mb-8">Service Frequency Configuration</h1>
      <ServiceFrequency :schedule-config="scheduleConfig" />
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 1200px;
}
</style>
