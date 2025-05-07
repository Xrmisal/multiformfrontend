<script setup>
import { ref } from 'vue';
import axios from 'axios'

const emit = defineEmits(['complete'])

const name = ref('')
const email = ref('')
const phone = ref('')
const loading = ref(false)
const errors = ref({name: '',email: '',phone: ''})

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
    errors.value = null
    if (!validateForm()) return;

    loading.value = true

    try {
        console.log("Trying")
        const response = await axios.post('/api/start', {
            name: name.value,
            email: email.value,
            phone: phone.value
        })

        const customerId = response.data.id
        const step = response.data.step

        localStorage.setItem('customerId', customerId)

        emit('complete', { id: customerId, step })
    } catch (err) {
        console.log("Failing")
        error.value = err.response?.data?.error || 'Something went wrong'
    } finally {
        loading.value = false
    }
}

</script>

<template>
    <div>
        <h2>Step 0: Start Form</h2>

        <div class="form-group">
        <label>Name</label>
        <input v-model="name" type="text" placeholder="Enter your name" />
        <span v-if="errors.name" class="error">{{ errors.name }}</span>
        </div>

        <div class="form-group">
        <label>Email</label>
        <input v-model="email" type="email" placeholder="Enter your email" />
        <span v-if="errors.email" class="error">{{ errors.email }}</span>
        </div>

        <div class="form-group">
        <label>Phone</label>
        <input v-model="phone" type="tel" placeholder="Enter your phone number" />
        <span v-if="errors.phone" class="error">{{ errors.phone }}</span>
        </div>

        <button @click="submit" :disabled="loading">
        {{ loading ? 'Submitting...' : 'Next' }}
        </button>
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