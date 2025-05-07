<script setup>
import { ref } from 'vue';
import axios from 'axios'

const emit = defineEmits(['complete'])

const name = ref('')
const email = ref('')
const phone = ref('')
const loading = ref(false)
const error = ref(null)
async function submit() {
    error.value = null
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

        <div v-if="error" class="error">{{ error }}</div>

        <input v-model="name" placeholder="Name" />
        <input v-model="email" placeholder="Email" />
        <input v-model="phone" placeholder="Phone" />

        <button @click="submit" :disabled="loading">
            {{ loading ? 'Submitting...' : 'Submit' }}
        </button>
    </div>
</template>

<style scoped>
.error{
    color: red;
    margin-bottom: 1rem;
}
</style>