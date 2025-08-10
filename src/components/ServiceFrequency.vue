<template>
  <div class="w-full max-w-2xl mx-auto space-y-6">
    <Card>
      <CardHeader>
        <CardTitle>Service Frequency</CardTitle>
      </CardHeader>
      <CardContent>
        <div class="my-4">
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
            <div>
              <div class="mb-6">
                <Label class="text-sm font-medium mb-3 block">Select Days:</Label>
                <div class="grid grid-cols-3 gap-2">
                  <Checkbox
                    v-for="day in days"
                    :key="day.value"
                    :id="day.value"
                    :label="day.label"
                    :modelValue="options.days.includes(day.value)"
                    @update:modelValue="handleDayToggle(day.value, $event)"
                  />
                </div>
              </div>

              <!-- Time Options -->
              <div>
                <Label class="text-sm font-medium text-gray-900 block mb-3">Time Constraints:</Label>
                
                <RadioGroup>
                  <RadioGroupItem
                    id="no-time-constraint"
                    name="timeConstraint"
                    value="none"
                    :checked="options.timeConstraint === 'none'"
                    @update:modelValue="handleTimeConstraintChange('none')"
                  >
                    No time constraint
                  </RadioGroupItem>
                  
                  <RadioGroupItem
                    id="time-window"
                    name="timeConstraint"
                    value="window"
                    :checked="options.timeConstraint === 'window'"
                    @update:modelValue="handleTimeConstraintChange('window')"
                  >
                    Specify time window(s)
                  </RadioGroupItem>
                  
                  <RadioGroupItem
                    id="exact-time"
                    name="timeConstraint"
                    value="exact"
                    :checked="options.timeConstraint === 'exact'"
                    @update:modelValue="handleTimeConstraintChange('exact')"
                  >
                    Set exact time
                  </RadioGroupItem>
                </RadioGroup>
                
                <!-- Time Window Input -->
                <div v-if="options.timeConstraint === 'window'" class="ml-6 mt-3">
                  <div class="grid grid-cols-3 gap-4 mb-3">
                    <div class="text-xs font-medium text-gray-700">From</div>
                    <div class="text-xs font-medium text-gray-700">To</div>
                    <div></div>
                  </div>
                  
                  <div v-for="(window, index) in options.timeWindows" :key="index" class="grid grid-cols-3 gap-4 items-center mb-2">
                    <Vue3Timepicker
                      v-model="window.from"
                      format="HH:mm"
                      placeholder=" "
                    />
                    <Vue3Timepicker
                      v-model="window.to"
                      format="HH:mm"
                      placeholder=" "
                    />
                    <button
                      v-if="options.timeWindows.length > 1"
                      @click="removeTimeWindow(index)"
                      class="text-xs text-red-600 hover:text-red-800"
                      type="button"
                    >
                      Remove
                    </button>
                  </div>
                  
                  <button
                    @click="addTimeWindow"
                    class="mt-2 text-xs text-blue-600 hover:text-blue-800"
                    type="button"
                  >
                    + Add time window
                  </button>
                </div>
                
                <!-- Exact Time Input -->
                <div v-if="options.timeConstraint === 'exact'" class="ml-6 mt-3">
                  <Label class="text-xs text-gray-500 mr-4">Specific time</Label>
                  <Vue3Timepicker
                    v-model="options.exactTime"
                    format="HH:mm"
                    placeholder=" "
                    class="mt-1"
                  />
                </div>
              </div>
            </div>
          </div>
          
          <!-- Save Button -->
          <div class="mt-6 flex justify-start">
            <Button @click="handleSave">
              Save
            </Button>
          </div>
        </div>
      </CardContent>
    </Card>

    <!-- JSON Output Display -->
    <Card>
      <CardHeader>
        <CardTitle class="text-lg text-gray-900">JSON</CardTitle>
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
import Vue3Timepicker from 'vue3-timepicker'
import Card from './ui/Card.vue'
import CardHeader from './ui/CardHeader.vue'
import CardContent from './ui/CardContent.vue'
import CardTitle from './ui/CardTitle.vue'
import RadioGroup from './ui/RadioGroup.vue'
import RadioGroupItem from './ui/RadioGroupItem.vue'
import Checkbox from './ui/Checkbox.vue'
import Label from './ui/Label.vue'
import Button from './ui/Button.vue'

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
const options = ref({
  days: [],
  timeConstraint: 'none', // 'none', 'window', 'exact'
  timeWindows: [{ from: '', to: '' }],
  exactTime: ''
})

const jsonOutput = computed(() => {
  const config = {
    frequency: selectedFrequency.value
  }

  if (selectedFrequency.value === 'custom') {
    config.options = {
      days: options.value.days
    }

    if (options.value.timeConstraint === 'window') {
      const validWindows = options.value.timeWindows
        .filter(window => window.from && window.to)
      
      if (validWindows.length > 0) {
        config.options.windows = validWindows
      }
    }

    if (options.value.timeConstraint === 'exact' && options.value.exactTime) {
      config.options.exactTime = options.value.exactTime
    }
  }

  return config
})

const handleFrequencyChange = (frequency) => {
  selectedFrequency.value = frequency
  
  // Reset custom options when switching away from custom
  if (frequency !== 'custom') {
    options.value = {
      days: [],
      timeConstraint: 'none',
      timeWindows: [{ from: '', to: '' }],
      exactTime: ''
    }
  }
}

const handleDayToggle = (day, checked) => {
  if (checked) {
    if (!options.value.days.includes(day)) {
      options.value.days.push(day)
    }
  } else {
    const index = options.value.days.indexOf(day)
    if (index > -1) {
      options.value.days.splice(index, 1)
    }
  }
}

const handleTimeConstraintChange = (constraint) => {
  options.value.timeConstraint = constraint
  
  // Clear/initialize time values when switching constraint types
  if (constraint === 'none') {
    options.value.timeWindows = [{ from: '', to: '' }]
    options.value.exactTime = ''
  } else if (constraint === 'window') {
    options.value.timeWindows = [{ from: '', to: '' }]
    options.value.exactTime = ''
  } else if (constraint === 'exact') {
    options.value.timeWindows = [{ from: '', to: '' }]
  }
}

const addTimeWindow = () => {
  options.value.timeWindows.push({ from: '', to: '' })
}

const removeTimeWindow = (index) => {
  if (options.value.timeWindows.length > 1) {
    options.value.timeWindows.splice(index, 1)
  }
}

const handleSave = () => {
  console.log('Saving configuration:', jsonOutput.value)
  // Add your save logic here
}

const parseScheduleConfig = (configString) => {
  if (!configString || configString.trim() === '') {
    return
  }

  try {
    const config = JSON.parse(configString)
    selectedFrequency.value = config.frequency || 'fortnightly'
    
    if (config.options && selectedFrequency.value === 'custom') {
      options.value.days = config.options.days || []
      
      if (config.options.windows) {
        options.value.timeConstraint = 'window'
        
        if (Array.isArray(config.options.windows)) {
          options.value.timeWindows = config.options.windows.filter(
            window => typeof window === 'object' && window.from && window.to
          )
          
          // Ensure at least one empty window if none are valid
          if (options.value.timeWindows.length === 0) {
            options.value.timeWindows = [{ from: '', to: '' }]
          }
        }
      } else if (config.options.exactTime) {
        options.value.timeConstraint = 'exact'
        options.value.exactTime = config.options.exactTime
      } else {
        options.value.timeConstraint = 'none'
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