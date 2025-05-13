<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import Step0 from './components/Step0.vue'
import Step1 from './components/Step1.vue'
import Step2 from './components/Step2.vue'
import Step3 from './components/Step3.vue'
import Success from './components/Success.vue'
import { errorMessages } from 'vue/compiler-sfc'

axios.defaults.withCredentials = true;

const currentStep = ref(0)
const message = ref(null)
const isLoading = ref(true)

const resumingMessage = ref(false)

onMounted(async () => {
try {
  const response = await axios.get(`http://localhost:8000/api/status`, {
    withCredentials: true
  })

  currentStep.value = response.data.step;
  resumingMessage.value = true;
  setTimeout(() => {
    resumingMessage.value = false;
  }, 5000)
} catch (err) {
  if (err.response && err.response.status === 422) {
    errorMessages.value = err.response.data.message
  } else {
    console.error('Restoration failed: ', err)
  }

  currentStep.value = 0;
}
    isLoading.value = false

});

  function handleStepComplete(payload) {
    currentStep.value = payload.step
}
</script>

<template>
  <div class="min-h-screen bg-gray-900 text-white font-sans">
  <div class="sticky top-0 z-50 w-full h-2 bg-gray-800">
    <div
      :style="`width: ${(currentStep + 1) * 25}%`"
      class="h-full bg-teal-500 transition-all duration-500"
    ></div>
  </div>


    <div class="max-w-xl mx-auto p-6">
      <!-- Sticky Header Title -->
    <div class="sticky top-2 z-40 bg-gray-900 py-4">
      <h1 class="text-3xl font-bold text-center text-teal-400">
        Multi-Step Form
      </h1>
    </div>


      <transition name="fade" mode="out-in">
        <Step0 v-if="currentStep === 0" @complete="handleStepComplete" />
        <Step1 v-else-if="currentStep === 1" @complete="handleStepComplete" />
        <Step2 v-else-if="currentStep === 2" @complete="handleStepComplete" />
        <Step3 v-else-if="currentStep === 3" @complete="handleStepComplete" />
        <Success v-else-if="currentStep === 4" />
      </transition>

      <p
        v-if="resumingMessage"
        class="mt-4 text-sm text-yellow-300 text-center animate-pulse"
      >
        Resuming your application...
      </p>
      <p v-if="error" class="mt-4 text-red-400 text-center">{{ error }}</p>
    </div>
  </div>
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>

