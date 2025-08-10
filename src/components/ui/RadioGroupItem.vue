<template>
  <div class="flex items-center space-x-3">
    <input
      :id="id"
      type="radio"
      :name="name"
      :value="value"
      :checked="checked"
      @change="$emit('update:modelValue', value)"
      class="h-4 w-4 border-gray-300 text-blue-600 focus:ring-blue-500"
    />
    <label
      :for="id"
      class="text-sm font-medium text-gray-900 cursor-pointer"
    >
      <slot />
    </label>
  </div>
</template>

<script setup>
import { cn } from '@/lib/utils'

defineProps({
  id: String,
  name: String,
  value: [String, Number],
  checked: Boolean
})

defineEmits(['update:modelValue'])
</script>