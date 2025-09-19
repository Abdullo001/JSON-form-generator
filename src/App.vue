<template>
  <v-app>
    <v-app-bar color="primary" dark>
      <v-app-bar-title>
        <v-icon class="mr-2">mdi-form-select</v-icon>
        Dynamic Form Generator
      </v-app-bar-title>
    </v-app-bar>

    <v-main>
      <v-container fluid class="pa-8">
        <v-row justify="center">
          <v-col cols="12" lg="10" xl="8">
            <v-card class="mb-6" elevation="2">
              <v-card-text class="pa-4">
                <v-row align="center">
                  <v-col cols="12" sm="6">
                    <v-select
                      v-model="currentForm"
                      :items="formOptions"
                      item-title="label"
                      item-value="value"
                      label="Select Form Type"
                      variant="outlined"
                      density="comfortable"
                      @update:model-value="switchForm"
                    />
                  </v-col>
                  <v-col cols="12" sm="6" class="text-center">
                    <v-chip
                      color="primary"
                      variant="tonal"
                      size="large"
                    >
                      <v-icon start>mdi-form-select</v-icon>
                      {{ formOptions.find(f => f.value === currentForm)?.label }}
                    </v-chip>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>

            <v-card class="mb-6" elevation="2">
              <v-card-title class="text-h4 text-center pa-6">
                {{ formConfig.title }}
              </v-card-title>
              <v-card-subtitle class="text-center pb-4">
                {{ formConfig.description }}
              </v-card-subtitle>
            </v-card>

            <v-card elevation="2">
              <v-card-text class="pa-6">
                <FormRenderer 
                  :config="formConfig" 
                  @submit="handleFormSubmit"
                />
              </v-card-text>
            </v-card>

            <v-card 
              v-if="showSuccess" 
              class="mt-6" 
              color="success" 
              variant="tonal"
              elevation="2"
            >
              <v-card-text class="text-center pa-4">
                <v-icon class="mr-2">mdi-check-circle</v-icon>
                Form submitted successfully! Check the console for the data.
              </v-card-text>
            </v-card>

            <v-card class="mt-6" elevation="2">
              <v-card-title class="d-flex align-center">
                <v-icon class="mr-2">mdi-code-json</v-icon>
                Form Configuration
                <v-spacer />
                <v-btn
                  variant="text"
                  size="small"
                  @click="showConfig = !showConfig"
                >
                  {{ showConfig ? 'Hide' : 'Show' }} JSON
                </v-btn>
              </v-card-title>
              <v-expand-transition>
                <v-card-text v-show="showConfig">
                  <v-textarea
                    :model-value="JSON.stringify(formConfig, null, 2)"
                    readonly
                    variant="outlined"
                    rows="20"
                    class="font-family-monospace"
                  />
                </v-card-text>
              </v-expand-transition>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <v-footer class="bg-grey-lighten-1">
      <v-row justify="center" no-gutters>
        <v-col
          class="text-center mt-4"
          cols="12"
        >
          <strong>Vue 3 + Vuetify Dynamic Form Generator</strong>
          <div class="text-caption mt-1">
            Built with Vue 3 Composition API and Vuetify 3
          </div>
        </v-col>
      </v-row>
    </v-footer>
  </v-app>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import FormRenderer from './components/FormRenderer.vue'
import formConfigData from './config/formConfig.json'
import simpleFormConfigData from './config/simpleFormConfig.json'

const formConfig = ref(formConfigData)
const showSuccess = ref(false)
const showConfig = ref(false)
const currentForm = ref('registration')

const formOptions = [
  { value: 'registration', label: 'User Registration', config: formConfigData },
  { value: 'contact', label: 'Contact Form', config: simpleFormConfigData }
]

const switchForm = (formType) => {
  const selectedForm = formOptions.find(form => form.value === formType)
  if (selectedForm) {
    formConfig.value = selectedForm.config
    showSuccess.value = false
  }
}

const handleFormSubmit = (formData) => {
  console.log('Form submitted with data:', formData)
  
  showSuccess.value = true
  
  setTimeout(() => {
    showSuccess.value = false
  }, 5000)
}

onMounted(() => {
  console.log('App mounted with form configuration:', formConfig.value)
})
</script>

<style scoped>
.font-family-monospace {
  font-family: 'Courier New', Courier, monospace;
  font-size: 12px;
}

.v-card {
  border-radius: 12px;
}

.v-app-bar {
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.v-footer {
  margin-top: auto;
}
</style>
