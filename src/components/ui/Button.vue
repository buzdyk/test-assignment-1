<template>
  <button
    class="inline-flex items-center justify-center rounded-md text-sm font-medium h-10 px-4 py-4 bg-blue-600 text-white hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 disabled:pointer-events-none disabled:opacity-50 transition-colors"
    v-bind="$attrs"
  >
    <slot />
  </button>
</template>

<script setup>
import { cn } from '@/lib/utils'
</script>