<script setup>
import { ref } from 'vue';
import axios from 'axios';

axios.defaults.withCredentials = true;

const dob = ref('');
const income = ref('');
const error = ref(null);
const loading = ref(false);

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
            `http://localhost:8000/api/update`,
            {
                dob: dob.value,
                income: income.value
            },
            {
                withCredentials: true
            }
        );

        emit('complete', {
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
    <div class="min-h-screen bg-gray-900 text-white flex items-center justify-center px-4">
    <div class="w-full max-w-md bg-gray-800 rounded-2xl shadow-lg p-6 space-y-6">
        <h2 class="text-2xl font-semibold text-center text-teal-400">3 / 5</h2>

        <div class="space-y-4">
        <div>
            <label for="dob" class="block text-sm font-medium">Date of Birth</label>
            <input v-model="dob" id="dob" type="date"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none" />
        </div>

        <div>
            <label for="income" class="block text-sm font-medium">Monthly Income (£)</label>
            <input v-model="income" id="income" type="number" placeholder="Enter your income"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none" />
        </div>

        <p v-if="error" class="text-sm text-red-500">{{ error }}</p>
        </div>

        <div>
        <button @click="submit" :disabled="loading"
            class="w-full bg-teal-500 hover:bg-teal-600 text-white font-semibold py-2 px-4 rounded-md transition disabled:opacity-50">
            {{ loading ? 'Submitting...' : 'Next' }}
        </button>
        </div>
    </div>
    </div>
</template>

<style scoped>

</style>