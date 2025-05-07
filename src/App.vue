<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import Step0 from './components/Step0.vue'
import Step1 from './components/Step1.vue'

const currentStep = ref(0)
const customerId = ref(localStorage.getItem('customerId') || null)
const message = ref(null)
const isLoading = ref(true)

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

    <Step0 v-if="!isLoading && currentStep === 0" @complete="handleStepComplete" />
    <Step1 v-if="!isLoading && currentStep === 1" />
  </div>
</template>

<style scoped>

</style>
