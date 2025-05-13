<script setup>
import { ref } from 'vue';
import axios from 'axios'

axios.defaults.withCredentials = true;

const emit = defineEmits(['complete'])

const name = ref('')
const email = ref('')
const phone = ref('')
const loading = ref(false)
const errors = ref({name: '',email: '',phone: '',server:''})

function validateForm() {
    let isValid = true;
    errors.value = {name: '', email: '', phone: ''};

    if (!name.value.trim()) {
        errors.value.name = 'Name is required.';
        isValid = false;
    }

    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!email.value.trim()) {
        errors.value.email = 'Email is required.';
        isValid = false;
    } else if (!emailPattern.test(email.value)) {
        errors.value.email = 'Invalid email format.';
        isValid = false;
    }

    const ukPhonePattern = /^(?:\+44|0)7\d{9}$/;
    if (!phone.value.trim()) {
        errors.value.phone = 'Phone number is required.';
        isValid = false;
    } else if (!ukPhonePattern.test(phone.value)) {
        errors.value.phone = 'Enter a valid UK phone number.';
        isValid = false;
    }

    return isValid;
}

async function submit() {
    if (!validateForm()) return;

    loading.value = true

    try {
        const response = await axios.post('/api/start', {
            name: name.value,
            email: email.value,
            phone: phone.value
        }, {
            withCredentials: true
        })

        emit('complete', {step: response.data.step})
    } catch (err) {
        errors.value.server = err.response?.data?.error || 'Something went wrong'
    } finally {
        loading.value = false
    }
}

</script>

<template>
    <div class="min-h-screen bg-gray-900 text-white flex items-center justify-center px-4">
    <div class="w-full max-w-md bg-gray-800 rounded-2xl shadow-lg p-6 space-y-6">
        <h2 class="text-2xl font-semibold text-center text-teal-400">1 / 5</h2>

        <div class="space-y-4">
        <div>
            <label class="block text-sm font-medium">Name</label>
            <input v-model="name" type="text" placeholder="Enter your name"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none">
            <span v-if="errors.name" class="text-sm text-red-500">{{ errors.name }}</span>
        </div>

        <div>
            <label class="block text-sm font-medium">Email</label>
            <input v-model="email" type="email" placeholder="Enter your email"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none">
            <span v-if="errors.email" class="text-sm text-red-500">{{ errors.email }}</span>
        </div>

        <div>
            <label class="block text-sm font-medium">Phone</label>
            <input v-model="phone" type="tel" placeholder="Enter your phone number"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none">
            <span v-if="errors.phone" class="text-sm text-red-500">{{ errors.phone }}</span>
        </div>
        </div>

        <div>
        <button @click="submit" :disabled="loading"
            class="w-full bg-teal-500 hover:bg-teal-600 text-white font-semibold py-2 px-4 rounded-md transition disabled:opacity-50">
            {{ loading ? 'Submitting...' : 'Next' }}
        </button>
        <span v-if="errors.server" class="block mt-2 text-sm text-red-500">{{ errors.server }}</span>
        </div>
    </div>
    </div>
</template>

<style scoped>
    .form-group {
    display: flex;
    flex-direction: column;
    margin-bottom: 1rem;
    }

    input {
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 1rem;
    }

    input:focus {
    border-color: #42b983;
    outline: none;
    }

    label {
    margin-bottom: 0.25rem;
    font-weight: bold;
    }

    .error {
    color: #d9534f;
    font-size: 0.875rem;
    margin-top: 0.25rem;
    }

    button {
    padding: 0.6rem 1.2rem;
    font-size: 1rem;
    background-color: #42b983;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    }

    button:disabled {
    background-color: #a5d6c1;
    cursor: not-allowed;
    }
</style>