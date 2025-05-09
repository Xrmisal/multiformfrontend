<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

let customer
const loading = ref(true)
const error = ref(null)
const emit = defineEmits(['complete'])

const customerId = localStorage.getItem('customerId')
const token = localStorage.getItem('access_token')

onMounted(async () => {
    try {
        const response = await axios.get(`http://localhost:8000/api/customer/${customerId}`, {
            headers: {
                Authorization: `Bearer ${token}`
            }
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
        await axios.delete(`http://localhost:8000/api/delete/${customerId}`, {
            headers: {
                Authorization: `Bearer ${token}`
            }
        })
        localStorage.removeItem('access_token')
        localStorage.removeItem('customerId')
        emit('complete', { step: 0, message: 'Form restarted'})
    } catch(err) {
        error.value = err.response?.data?.error || 'Failed to restart form'
    }
}

async function submitFinal() {
    try {
        await axios.put(`http://localhost:8000/api/update/${customerId}`, {
            step: 3
        }, {
            headers: {
                Authorization: `Bearer ${token}`
            }
        })

        localStorage.removeItem('access_token')
        localStorage.removeItem('customerId')
        emit('complete', { step: 4, message: 'Form submitted'})
    } catch (err) {
        error.value = err.response?.data?.error || 'Failed to submit form'
    }
}
</script>

<template>
<div>
    <h2>Step 3: Review Your Information</h2>

    <div v-if="loading">Loading...</div>
    <div v-else-if="error" class="text-red">{{ error }}</div>
    <div v-else-if="customer">
    <ul>
        <li><strong>Name:</strong> {{ customer.name }}</li>
        <li><strong>Email:</strong> {{ customer.email }}</li>
        <li><strong>Phone:</strong> {{ customer.phone }}</li>
        <li><strong>Address:</strong> {{ customer.address }}</li>
        <li><strong>Postcode:</strong> {{ customer.postcode }}</li>
        <li><strong>Date of Birth:</strong> {{ customer.dob }}</li>
        <li><strong>Monthly Income:</strong> £{{ customer.income }}</li>
    </ul>

    <button @click="restartForm" style="margin-right: 1rem; background-color: crimson; color: white;">
        Start Over
    </button>
    <button @click="submitFinal" style="background-color: green; color: white;">
        Confirm & Submit
    </button>
    </div>
</div>
</template>

<style scoped>
button {
    padding: 0.6rem 1rem;
    font-size: 1rem;
    background-color: #35495e;
    color: white;
    border: none;
    cursor: pointer;
    margin-top: 1rem;
}
</style>