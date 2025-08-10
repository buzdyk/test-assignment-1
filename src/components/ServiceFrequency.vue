<template>
  <div class="w-full max-w-2xl mx-auto space-y-6">
    <Card>
      <CardHeader>
        <CardTitle>Service Frequency</CardTitle>
      </CardHeader>
      <CardContent>
        <div class="space-y-4">
          <RadioGroup>
            <RadioGroupItem
              id="weekly"
              name="frequency"
              value="weekly"
              :checked="selectedFrequency === 'weekly'"
              @update:modelValue="handleFrequencyChange('weekly')"
            >
              Weekly
            </RadioGroupItem>
            
            <RadioGroupItem
              id="fortnightly"
              name="frequency"
              value="fortnightly"
              :checked="selectedFrequency === 'fortnightly'"
              @update:modelValue="handleFrequencyChange('fortnightly')"
            >
              Fortnightly
            </RadioGroupItem>
            
            <RadioGroupItem
              id="every-4-weeks"
              name="frequency"
              value="every-4-weeks"
              :checked="selectedFrequency === 'every-4-weeks'"
              @update:modelValue="handleFrequencyChange('every-4-weeks')"
            >
              Every 4 weeks
            </RadioGroupItem>
            
            <RadioGroupItem
              id="none"
              name="frequency"
              value="none"
              :checked="selectedFrequency === 'none'"
              @update:modelValue="handleFrequencyChange('none')"
            >
              None
            </RadioGroupItem>
            
            <RadioGroupItem
              id="custom"
              name="frequency"
              value="custom"
              :checked="selectedFrequency === 'custom'"
              @update:modelValue="handleFrequencyChange('custom')"
            >
              Custom
            </RadioGroupItem>
          </RadioGroup>

          <!-- Custom Options Section -->
          <div v-if="selectedFrequency === 'custom'" class="mt-6 p-4 border border-gray-200 rounded-lg bg-gray-50">
            <h4 class="font-medium mb-4 text-gray-900">Custom Schedule Options</h4>
            
            <!-- Days Selection -->
            <div class="space-y-4">
              <div>
                <Label class="text-sm font-medium mb-3 block">Select Days:</Label>
                <div class="grid grid-cols-2 gap-2">
                  <Checkbox
                    v-for="day in days"
                    :key="day.value"
                    :id="day.value"
                    :label="day.label"
                    :modelValue="customOptions.days.includes(day.value)"
                    @update:modelValue="handleDayToggle(day.value, $event)"
                  />
                </div>
              </div>

              <!-- Time Window Options -->
              <div class="space-y-3">
                <Checkbox
                  id="time-windows"
                  label="Specify time window(s)"
                  :modelValue="customOptions.hasTimeWindows"
                  @update:modelValue="handleTimeWindowToggle"
                />
                
                <div v-if="customOptions.hasTimeWindows" class="ml-6">
                  <Label for="time-window-input" class="text-sm text-gray-600">
                    Enter time ranges (e.g., 06:30–07:30,15:00-17:00):
                  </Label>
                  <Input
                    id="time-window-input"
                    v-model="customOptions.timeWindowsInput"
                    placeholder="06:30–07:30,15:00-17:00"
                    class="mt-1"
                  />
                </div>
              </div>

              <!-- Exact Time Option -->
              <div class="space-y-3">
                <Checkbox
                  id="exact-time"
                  label="Set exact time"
                  :modelValue="customOptions.hasExactTime"
                  @update:modelValue="handleExactTimeToggle"
                />
                
                <div v-if="customOptions.hasExactTime" class="ml-6">
                  <Label for="exact-time-input" class="text-sm text-gray-600">
                    Enter specific time:
                  </Label>
                  <Input
                    id="exact-time-input"
                    v-model="customOptions.exactTime"
                    type="time"
                    class="mt-1"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </CardContent>
    </Card>

    <!-- JSON Output Display -->
    <Card>
      <CardHeader>
        <CardTitle class="text-lg text-gray-900">JSON Configuration</CardTitle>
      </CardHeader>
      <CardContent>
        <div class="bg-gray-100 p-4 rounded-md">
          <pre class="text-sm overflow-x-auto">{{ JSON.stringify(jsonOutput, null, 2) }}</pre>
        </div>
      </CardContent>
    </Card>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import Card from './ui/Card.vue'
import CardHeader from './ui/CardHeader.vue'
import CardContent from './ui/CardContent.vue'
import CardTitle from './ui/CardTitle.vue'
import RadioGroup from './ui/RadioGroup.vue'
import RadioGroupItem from './ui/RadioGroupItem.vue'
import Checkbox from './ui/Checkbox.vue'
import Input from './ui/Input.vue'
import Label from './ui/Label.vue'

const props = defineProps({
  scheduleConfig: {
    type: String,
    default: ''
  }
})

const days = [
  { value: 'monday', label: 'Monday' },
  { value: 'tuesday', label: 'Tuesday' },
  { value: 'wednesday', label: 'Wednesday' },
  { value: 'thursday', label: 'Thursday' },
  { value: 'friday', label: 'Friday' },
  { value: 'saturday', label: 'Saturday' },
  { value: 'sunday', label: 'Sunday' }
]

const selectedFrequency = ref('fortnightly')
const customOptions = ref({
  days: [],
  hasTimeWindows: false,
  timeWindowsInput: '',
  hasExactTime: false,
  exactTime: ''
})

const jsonOutput = computed(() => {
  const config = {
    frequency: selectedFrequency.value
  }

  if (selectedFrequency.value === 'custom') {
    config.customOptions = {
      days: customOptions.value.days
    }

    if (customOptions.value.hasTimeWindows && customOptions.value.timeWindowsInput) {
      config.customOptions.timeWindows = customOptions.value.timeWindowsInput
        .split(',')
        .map(window => window.trim())
        .filter(window => window.length > 0)
    }

    if (customOptions.value.hasExactTime && customOptions.value.exactTime) {
      config.customOptions.exactTime = customOptions.value.exactTime
    }
  }

  return config
})

const handleFrequencyChange = (frequency) => {
  selectedFrequency.value = frequency
  
  // Reset custom options when switching away from custom
  if (frequency !== 'custom') {
    customOptions.value = {
      days: [],
      hasTimeWindows: false,
      timeWindowsInput: '',
      hasExactTime: false,
      exactTime: ''
    }
  }
}

const handleDayToggle = (day, checked) => {
  if (checked) {
    if (!customOptions.value.days.includes(day)) {
      customOptions.value.days.push(day)
    }
  } else {
    const index = customOptions.value.days.indexOf(day)
    if (index > -1) {
      customOptions.value.days.splice(index, 1)
    }
  }
}

const handleTimeWindowToggle = (checked) => {
  customOptions.value.hasTimeWindows = checked
  if (!checked) {
    customOptions.value.timeWindowsInput = ''
  }
}

const handleExactTimeToggle = (checked) => {
  customOptions.value.hasExactTime = checked
  if (!checked) {
    customOptions.value.exactTime = ''
  }
}

const parseScheduleConfig = (configString) => {
  if (!configString || configString.trim() === '') {
    return
  }

  try {
    const config = JSON.parse(configString)
    selectedFrequency.value = config.frequency || 'fortnightly'
    
    if (config.customOptions && selectedFrequency.value === 'custom') {
      customOptions.value.days = config.customOptions.days || []
      
      if (config.customOptions.timeWindows) {
        customOptions.value.hasTimeWindows = true
        customOptions.value.timeWindowsInput = Array.isArray(config.customOptions.timeWindows) 
          ? config.customOptions.timeWindows.join(',')
          : config.customOptions.timeWindows
      }
      
      if (config.customOptions.exactTime) {
        customOptions.value.hasExactTime = true
        customOptions.value.exactTime = config.customOptions.exactTime
      }
    }
  } catch (error) {
    console.warn('Invalid schedule config JSON:', error)
    selectedFrequency.value = 'fortnightly'
  }
}

onMounted(() => {
  parseScheduleConfig(props.scheduleConfig)
})

watch(() => props.scheduleConfig, (newConfig) => {
  parseScheduleConfig(newConfig)
})
</script>