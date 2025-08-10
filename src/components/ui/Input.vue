<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
    :type="type"
    :placeholder="placeholder"
    class="flex h-10 w-full rounded-md border border-gray-300 bg-white px-3 py-2 text-sm text-gray-900 placeholder-gray-500 focus:border-blue-500 focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50 disabled:cursor-not-allowed disabled:opacity-50"
  />
</template>

<script setup>
import { cn } from '@/lib/utils'

defineProps({
  type: {
    type: String,
    default: 'text'
  },
  placeholder: String,
  modelValue: [String, Number]
})

defineEmits(['update:modelValue'])
</script>