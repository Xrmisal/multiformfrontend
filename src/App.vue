<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import Step0 from './components/Step0.vue'
import Step1 from './components/Step1.vue'
import Step2 from './components/Step2.vue'

const currentStep = ref(0)
const customerId = ref(localStorage.getItem('customerId') || null)
const message = ref(null)
const isLoading = ref(true)

const resumingMessage = ref(false)

onMounted(async () => {
  const storedId = localStorage.getItem('customerId');

  if (!storedId) {
    currentStep.value = 0;
    isLoading.value = false;
    return;
  }

  customerId.value = storedId

  if (customerId.value) {
    try {
      const response = await axios.get(`http://localhost:8000/api/status/${customerId.value}`)
      currentStep.value = response.data.step
      resumingMessage.value = true

      setTimeout(() => {
        resumingMessage.value = false
      }, 5000)
    } catch (err) {
      console.error('Restoration failed:', err)
      localStorage.removeItem('customerId')
      customerId.value = null
      currentStep.value = 0
    }
    isLoading.value = false
  }
})

  function handleStepComplete(payload) {
    if (!customerId.value && payload.id) {
      customerId.value = payload.id
      localStorage.setItem('customerId', payload.id)
    }
    currentStep.value = payload.step

    if (payload.message) {
      message.value = payload.message
    }
}
</script>

<template>
  <div>
    <h1>Multi-Step Form</h1>
    <p v-if="resumingMessage" class="resuming-banner">Resuming your application...</p>

    <Step0 v-if="!isLoading && currentStep === 0" @complete="handleStepComplete" />
    <Step1 v-if="!isLoading && currentStep === 1" @complete="handleStepComplete"/>
    <Step2 v-if="!isLoading && currentStep === 2" @complete="handleStepComplete" />
  </div>
</template>

<style scoped>
.resuming-banner {
  background-color: #e8f5e9;
  color: #2e7d32;
  padding: 0.75rem;
  border-left: 4px solid #2e7d32;
  margin-bottom: 1rem;
  font-weight: bold;
  border-radius: 4px;
}
</style>
