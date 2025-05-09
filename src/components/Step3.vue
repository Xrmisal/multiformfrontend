<script setup>
import { defineEmits } from 'vue'

const emit = defineEmits(['complete'])

function goToNextStep() {
    emit('complete', {
        step: 4,
        message: 'Review your information'
    })
    }
</script>

<template>
<div>
    <h2>Step 3: Summary</h2>
    <p>This is just a placeholder. Eventually, this will show a summary of all submitted data for review.</p>
    <button @click="goToNextStep">Continue</button>
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