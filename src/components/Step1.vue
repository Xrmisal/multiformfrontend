<script setup>
import { getCurrentScope, ref } from 'vue';
import axios from 'axios';

const houseNumber = ref('')
const street = ref('')
const city = ref('')
const postcode = ref('')
const error = ref(null)
const loading = ref(false)
const validating = ref(false)

const errors = ref({ address: '', postcode: '' })
const emit = defineEmits(['complete'])

const customerId = localStorage.getItem('customerId')

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
        const res = await axios.get(`https://api.postcodes.io/postcodes/${cleanPostcode}`)
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
    const token = localStorage.getItem('access_token');
    if (!token) {
        error.value = 'Authorization token is missing';
        return;
    };
    loading.value = true

    const payload = {
    address,
    postcode: postcode.value
    };

    const config = {
    headers: {
        Authorization: `Bearer ${token}`
    }
    };

    try {

        const response = await axios.put(`http://localhost:8000/api/update/${customerId}`, payload, config);

        emit('complete', {
            id: customerId,
            step: response.data.step,
            message: response.data.message || null
        })
    } catch (err) {
        error.value = err.response?.data?.error || 'Something went wrong'
    } finally {
        loading.value = false;
    }
}

</script>

<template>
    <div>
        <h2>Step 1: Address</h2>

        <div class="form-group">
        <label>House/Flat Number</label>
        <input v-model="houseNumber" />
        <span v-if="errors.houseNumber" class="error">{{ errors.houseNumber }}</span>
        </div>

        <div class="form-group">
        <label>Street Name</label>
        <input v-model="street" />
        <span v-if="errors.street" class="error">{{ errors.street }}</span>
        </div>

        <div class="form-group">
        <label>City</label>
        <input v-model="city" />
        <span v-if="errors.city" class="error">{{ errors.city }}</span>
        </div>

        <div class="form-group">
            <label>Postcode</label>
            <input v-model="postcode" placeholder="Enter your postcode" />
            <span v-if="errors.postcode" class="error">{{ errors.postcode }}</span>
        </div>

        <button @click="submit" :disabled="loading || validating">
            {{ loading ? 'Submitting...' : validating ? 'Validating...' : 'Next' }}
        </button>

        <p v-if="error" class="error">{{ error }}</p>
    </div>
</template>

<style scoped>
    .form-group {
    margin-bottom: 1rem;
    display: flex;
    flex-direction: column;
    }

    input {
    padding: 0.5rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    }

    .error {
    color: red;
    font-size: 0.85rem;
    margin-top: 0.25rem;
    }

    button {
    padding: 0.6rem 1rem;
    background-color: #42b983;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    }
    button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
    }
</style>
