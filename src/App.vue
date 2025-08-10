<script setup>
import { ref } from 'vue'
import ServiceFrequency from './components/ServiceFrequency.vue'

const scheduleConfig = ref('')
</script>

<template>
  <div class="min-h-screen bg-gray-50 py-8">
    <div class="container mx-auto px-4 max-w-4xl">
      <ServiceFrequency :schedule-config="scheduleConfig" />
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 1200px;
}
</style>
