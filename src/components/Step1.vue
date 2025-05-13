<script setup>
import { getCurrentScope, ref } from 'vue';
import axios from 'axios';

axios.defaults.withCredentials = true;

const houseNumber = ref('')
const street = ref('')
const city = ref('')
const postcode = ref('')
const error = ref(null)
const loading = ref(false)
const validating = ref(false)

const errors = ref({ address: '', postcode: '' })
const emit = defineEmits(['complete'])

const ukPostcodePattern = /^([Gg][Ii][Rr] 0[Aa]{2})|((([A-Za-z][0-9]{1,2})|(([A-Za-z][A-Ha-hJ-Yj-y][0-9]{1,2})|(([A-Za-z][0-9][A-Za-z])|([A-Za-z][A-Ha-hJ-Yj-y][0-9]?[A-Za-z]?))))\s?[0-9][A-Za-z]{2})$/

async function validateForm() {
    errors.value = { address: '', postcode: '' }
    let isValid = true

    if(!houseNumber.value.trim()) {
        errors.value.houseNumber = 'House number is required'
        isValid = false
    }

    if (!street.value.trim()) {
        errors.value.street = 'Street name is required.'
        isValid = false
    }

    if (!city.value.trim()) {
        errors.value.city = 'City is required.'
        isValid = false
    }

    const cleanPostcode = postcode.value.trim().toUpperCase()

    if (!cleanPostcode) {
        errors.value.postcode = 'Postcode is required.'
        isValid = false
    } else if (!ukPostcodePattern.test(cleanPostcode)) {
        errors.value.postcode = 'Invalid postcode format.'
        isValid = false
    } else {
        // Check postcode exists using postcodes.io
        validating.value = true
        try {
        const res = await axios.get(`https://api.postcodes.io/postcodes/${cleanPostcode}`, {
            withCredentials: false
        })
        if (res.data.status !== 200) {
            errors.value.postcode = 'Postcode does not exist.'
            isValid = false
        }
        } catch (err) {
        errors.value.postcode = 'Failed to verify postcode.'
        isValid = false
        } finally {
        validating.value = false
        }
    }

    return isValid
}

async function submit () {
    error.value = null
    if (!(await validateForm())) return

    const address = `${houseNumber.value} ${street.value}, ${city.value}`
    loading.value = true

    const payload = {
    address,
    postcode: postcode.value
    };

    try {

        const response = await axios.put(`http://localhost:8000/api/update`, payload, {
            withCredentials: true,
            headers: {
                'Content-Type': 'application/json'
            }
        });

        emit('complete', {
            step: response.data.step,
        })
    } catch (err) {
        error.value = err.response?.data?.error || 'Something went wrong'
    } finally {
        loading.value = false;
    }
}

</script>

<template>
    <div class="min-h-screen bg-gray-900 text-white flex items-center justify-center px-4">
    <div class="w-full max-w-md bg-gray-800 rounded-2xl shadow-lg p-6 space-y-6">
        <h2 class="text-2xl font-semibold text-center text-teal-400">2 / 5</h2>

        <div class="space-y-4">
        <div>
            <label class="block text-sm font-medium">House/Flat Number</label>
            <input v-model="houseNumber" type="text" placeholder="Enter your number"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none" />
            <span v-if="errors.houseNumber" class="text-sm text-red-500">{{ errors.houseNumber }}</span>
        </div>

        <div>
            <label class="block text-sm font-medium">Street Name</label>
            <input v-model="street" type="text" placeholder="Enter your street"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none" />
            <span v-if="errors.street" class="text-sm text-red-500">{{ errors.street }}</span>
        </div>

        <div>
            <label class="block text-sm font-medium">City</label>
            <input v-model="city" type="text" placeholder="Enter your city"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none" />
            <span v-if="errors.city" class="text-sm text-red-500">{{ errors.city }}</span>
        </div>

        <div>
            <label class="block text-sm font-medium">Postcode</label>
            <input v-model="postcode" type="text" placeholder="Enter your postcode"
            class="mt-1 w-full px-4 py-2 rounded-md bg-gray-700 text-white border border-gray-600 focus:ring-2 focus:ring-teal-500 outline-none" />
            <span v-if="errors.postcode" class="text-sm text-red-500">{{ errors.postcode }}</span>
        </div>

        <p v-if="error" class="text-sm text-red-500">{{ error }}</p>
        </div>

        <div>
        <button @click="submit" :disabled="loading || validating"
            class="w-full bg-teal-500 hover:bg-teal-600 text-white font-semibold py-2 px-4 rounded-md transition disabled:opacity-50">
            {{ loading ? 'Submitting...' : validating ? 'Validating...' : 'Next' }}
        </button>
        </div>
    </div>
    </div>
</template>

<style scoped>

</style>
