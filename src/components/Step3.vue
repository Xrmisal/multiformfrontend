<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

axios.defaults.withCredentials = true;

let customer
const loading = ref(true)
const error = ref(null)
const emit = defineEmits(['complete'])


onMounted(async () => {
    try {
        const response = await axios.get(`http://localhost:8000/api/customer`, {
            withCredentials: true
        })
        customer = response.data;

    } catch (err) {
        error.value = err.response?.data?.error || 'Failed to load information'
    } finally {
        loading.value = false
    }
})

async function restartForm() {
    try {
        await axios.delete(`http://localhost:8000/api/delete`, {
            withCredentials:true
        })
        window.location.reload()
        emit('complete', { step: 0, })
    } catch(err) {
        error.value = err.response?.data?.error || 'Failed to restart form'
    }
}

async function submitFinal() {
    try {
        await axios.put(`http://localhost:8000/api/update`, {
            step: 3
        }, {
            withCredentials: true
        })
        emit('complete', {
            step: 4,
        })
    } catch (err) {
        error.value = err.response?.data?.error || 'Failed to submit form'
    }
}
</script>

<template>
    <div v-if="loading"></div>
    <div v-else-if="customer"class="min-h-screen bg-gray-900 text-white flex items-center justify-center px-4">
    <div class="w-full max-w-xl bg-gray-800 rounded-2xl shadow-lg p-8 space-y-6">
        <h2 class="text-2xl font-semibold text-center text-teal-400">4 / 5</h2>
        <p class="text-center text-gray-400">Please review your information before submitting</p>

        <ul class="space-y-4">
        <li><strong class="text-teal-400">Name:</strong> {{ customer.name }}</li>
        <li><strong class="text-teal-400">Email:</strong> {{ customer.email }}</li>
        <li><strong class="text-teal-400">Phone:</strong> {{ customer.phone }}</li>
        <li><strong class="text-teal-400">Address:</strong> {{ customer.address }}</li>
        <li><strong class="text-teal-400">Postcode:</strong> {{ customer.postcode }}</li>
        <li><strong class="text-teal-400">Date of Birth:</strong> {{ customer.dob }}</li>
        <li><strong class="text-teal-400">Monthly Income:</strong> £{{ customer.income }}</li>
        </ul>

        <p v-if="error" class="text-sm text-red-500 text-center">{{ error }}</p>

        <div class="flex justify-between mt-6">
        <button
            @click="restartForm"
            class="bg-red-500 hover:bg-red-600 text-white font-semibold py-2 px-4 rounded-md transition">
            Start Over
        </button>

        <button
            @click="submitFinal"
            class="bg-teal-500 hover:bg-teal-600 text-white font-semibold py-2 px-4 rounded-md transition">
            Confirm & Submit
        </button>
        </div>
    </div>
    </div>
</template>

<style scoped>

</style>