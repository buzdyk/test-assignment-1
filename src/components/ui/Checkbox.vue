<template>
  <div class="flex items-center space-x-3">
    <input
      :id="id"
      type="checkbox"
      :checked="modelValue"
      @change="$emit('update:modelValue', $event.target.checked)"
      class="h-4 w-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500"
    />
    <label
      v-if="$slots.default || label"
      :for="id"
      class="text-sm font-medium text-gray-900 cursor-pointer"
    >
      <slot>{{ label }}</slot>
    </label>
  </div>
</template>

<script setup>
import { cn } from '@/lib/utils'

defineProps({
  id: String,
  label: String,
  modelValue: Boolean
})

defineEmits(['update:modelValue'])
</script>