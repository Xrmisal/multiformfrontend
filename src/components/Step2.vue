<script setup>
import { ref } from 'vue';
import axios from 'axios';

const dob = ref('');
const income = ref('');
const error = ref(null);
const loading = ref(false);

const customerId = localStorage.getItem('customerId');
const token = localStorage.getItem('access_token');
const emit = defineEmits(['complete']);

function validateForm() {
    error.value = null;

    if (!dob.value || !income.value) {
        error.value = 'Both fields are required.';
        return false;
    }

    const age = new Date().getFullYear() - new Date(dob.value).getFullYear();
    if (age < 18 || isNaN(age)) {
        error.value = 'You must be at least 18 years old.';
        return false;
    }

    if (isNaN(income.value) || Number(income.value) <= 1) {
        error.value = 'Income must be a number greater than 1.';
        return false;
    }

    return true;

}

async function submit() {
    if (!validateForm()) return;

    loading.value = true;

    try {
        const response = await axios.put(
            `http://localhost:8000/api/update/${customerId}`,
            {
                dob: dob.value,
                income: income.value
            },
            {
                headers: {
                    Authorization: `Bearer ${token}`,
                    'Content-Type': 'application/json'
                }
            }
        );

        emit('complete', {
            id: customerId,
            step: response.data.step
        });

    } catch (err) {
        error.value = err.response?.data?.error || 'Something went wrong';
    } finally {
        loading.value = false;
    }
}

</script>

<template>
    <div>
    <h2>Step 2: Final Step</h2>
    <div class="form-group">
        <label for="dob">Date of Birth</label>
        <input type="date" v-model="dob" />
    </div>

    <div class="form-group">
        <label for="income">Monthly Income (£)</label>
        <input type="number" v-model="income" />
    </div>

    <button @click="submit" :disabled="loading">
        {{ loading ? 'Submitting...' : 'Next' }}
    </button>

    <p v-if="error" class="error">{{ error }}</p>
    </div>
</template>

<style scoped>
    .form-group {
    margin-bottom: 1rem;
    }
    input {
    padding: 0.5rem;
    font-size: 1rem;
    }
    button {
    padding: 0.5rem 1rem;
    }
    .error {
    color: red;
    margin-top: 1rem;
    }
</style>