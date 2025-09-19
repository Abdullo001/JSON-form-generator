<template>
  <v-form ref="form" v-model="valid" @submit.prevent="handleSubmit">
    <v-container>
      <v-row v-for="(row, rowIndex) in formConfig.layout" :key="rowIndex">
        <v-col 
          v-for="(field, fieldIndex) in row.fields" 
          :key="fieldIndex"
          :cols="field.cols || 12"
          :sm="field.sm || 12"
          :md="field.md || 12"
          :lg="field.lg || 12"
        >
          <v-text-field
            v-if="field.type === 'text'"
            v-model="formData[field.name]"
            :label="field.label"
            :placeholder="field.placeholder"
            :rules="getValidationRules(field)"
            :required="field.required"
            :disabled="field.disabled"
            variant="outlined"
            density="comfortable"
          />

          <v-text-field
            v-else-if="field.type === 'email'"
            v-model="formData[field.name]"
            :label="field.label"
            :placeholder="field.placeholder"
            :rules="getValidationRules(field)"
            :required="field.required"
            :disabled="field.disabled"
            type="email"
            variant="outlined"
            density="comfortable"
          />

          <v-text-field
            v-else-if="field.type === 'number'"
            v-model="formData[field.name]"
            :label="field.label"
            :placeholder="field.placeholder"
            :rules="getValidationRules(field)"
            :required="field.required"
            :disabled="field.disabled"
            type="text"
            variant="outlined"
            density="comfortable"
            @keydown="validateNumberInput"
          />

          <v-select
            v-else-if="field.type === 'select'"
            v-model="formData[field.name]"
            :label="field.label"
            :items="getSelectOptions(field)"
            :rules="getValidationRules(field)"
            :required="field.required"
            :disabled="field.disabled"
            variant="outlined"
            density="comfortable"
            @update:model-value="handleSelectChange(field)"
            item-title="label"
            item-value="value"
          />

          <div v-else-if="field.type === 'radio'">
            <v-label class="text-subtitle-2 mb-2">{{ field.label }}</v-label>
            <v-radio-group
              v-model="formData[field.name]"
              :rules="getValidationRules(field)"
              :required="field.required"
              :disabled="field.disabled"
              inline
            >
              <v-radio
                v-for="option in field.options"
                :key="option.value"
                :label="option.label"
                :value="option.value"
              />
            </v-radio-group>
          </div>

          <v-checkbox
            v-else-if="field.type === 'checkbox'"
            v-model="formData[field.name]"
            :label="field.label"
            :rules="getValidationRules(field)"
            :required="field.required"
            :disabled="field.disabled"
            color="primary"
          />

          <v-text-field
            v-else-if="field.type === 'date'"
            v-model="formData[field.name]"
            :label="field.label"
            :rules="getValidationRules(field)"
            :required="field.required"
            :disabled="field.disabled"
            type="date"
            variant="outlined"
            density="comfortable"
          />

          <v-textarea
            v-else-if="field.type === 'textarea'"
            v-model="formData[field.name]"
            :label="field.label"
            :placeholder="field.placeholder"
            :rules="getValidationRules(field)"
            :required="field.required"
            :disabled="field.disabled"
            :rows="field.rows || 3"
            variant="outlined"
            density="comfortable"
          />
        </v-col>
      </v-row>

      <v-row class="mt-4">
        <v-col cols="12" class="text-center">
          <v-btn
            type="submit"
            color="primary"
            size="large"
            :disabled="!valid"
            :loading="isSubmitting"
          >
            {{ formConfig.submitButton?.text || 'Submit' }}
          </v-btn>
        </v-col>
      </v-row>
    </v-container>
  </v-form>
</template>

<script setup>
import { ref, reactive, watch, computed } from 'vue'

const props = defineProps({
  config: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['submit'])

const form = ref(null)
const valid = ref(false)
const isSubmitting = ref(false)
const formData = reactive({})

const initializeFormData = () => {
  props.config.layout.forEach(row => {
    row.fields.forEach(field => {
      if (field.type === 'checkbox') {
        formData[field.name] = field.defaultValue || false
      } else if (field.type === 'select' && field.multiple) {
        formData[field.name] = field.defaultValue || []
      } else {
        formData[field.name] = field.defaultValue || ''
      }
    })
  })
}

const getValidationRules = (field) => {
  const rules = []
  
  if (field.required) {
    rules.push(v => !!v || `${field.label} is required`)
  }
  
  if (field.type === 'email') {
    rules.push(v => !v || /.+@.+\..+/.test(v) || 'Email must be valid')
  }
  
  if (field.minLength) {
    rules.push(v => !v || v.length >= field.minLength || `Minimum ${field.minLength} characters`)
  }
  
  if (field.maxLength) {
    rules.push(v => !v || v.length <= field.maxLength || `Maximum ${field.maxLength} characters`)
  }
  
  if (field.min && field.type === 'number') {
    rules.push(v => !v || v >= field.min || `Minimum value is ${field.min}`)
  }
  
  if (field.max && field.type === 'number') {
    rules.push(v => !v || v <= field.max || `Maximum value is ${field.max}`)
  }
  
  return rules
}
const validateNumberInput = (event) => {
  const allowedKeys = [
    'Backspace', 'Delete', 'Tab', 'Escape', 'Enter',
    'ArrowLeft', 'ArrowRight', 'ArrowUp', 'ArrowDown',
    'Home', 'End', 'F5'
  ];
  
  if (event.ctrlKey || event.metaKey) {
    const allowedCtrlKeys = ['a', 'c', 'v', 'x', 'z', 'y'];
    if (allowedCtrlKeys.includes(event.key.toLowerCase())) {
      return;
    }
  }
  
  if (allowedKeys.includes(event.key)) {
    return;
  }
  
  if (/^[0-9]$/.test(event.key)) {
    return;
  }
  
  const currentValue = event.target.value;
  const cursorPosition = event.target.selectionStart;
  
  if (event.key === '-') {
    if (cursorPosition === 0 && !currentValue.includes('-')) {
      return;
    }
    event.preventDefault();
    return;
  }
  
  if (event.key === '.') {
    if (!currentValue.includes('.') && currentValue.length > 0) {
      return;
    }
    event.preventDefault();
    return;
  }
  
  event.preventDefault();
};

const getSelectOptions = (field) => {
  if (field.dependsOn) {
    const dependentValue = formData[field.dependsOn.field]
    if (dependentValue && field.dependencyOptions && field.dependencyOptions[dependentValue]) {
      return field.dependencyOptions[dependentValue]
    }
    return []
  }
  return field.options || []
}

const handleSelectChange = (field) => {
  props.config.layout.forEach(row => {
    row.fields.forEach(f => {
      if (f.dependsOn && f.dependsOn.field === field.name) {
        formData[f.name] = f.type === 'select' && f.multiple ? [] : ''
      }
    })
  })
}

const handleSubmit = async () => {
  if (!valid.value) return
  
  isSubmitting.value = true
  
  try {
    emit('submit', { ...formData })
    
    console.log('Form submitted with data:', formData)
    
    
  } catch (error) {
    console.error('Form submission error:', error)
  } finally {
    isSubmitting.value = false
  }
}

watch(() => props.config, () => {
  initializeFormData()
}, { immediate: true, deep: true })

const formConfig = computed(() => props.config)
</script>

<style scoped>
.v-form {
  max-width: 800px;
  margin: 0 auto;
}
</style>
