<template>
  <div :class="cn('grid gap-2', $attrs.class)" role="radiogroup">
    <slot />
  </div>
</template>

<script setup>
import { cn } from '@/lib/utils'
</script>