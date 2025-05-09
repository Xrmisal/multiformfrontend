<script setup>
import { onMounted } from 'vue'

onMounted(() => {
    localStorage.removeItem('access_token')
    localStorage.removeItem('customerId')
})
</script>

<template>
<div>
    <h2>✅ Application Submitted</h2>
    <p>Your multi-step form is complete. Thank you!</p>
    <p>You may now close this tab or start a new application.</p>
</div>
</template>

<style scoped>
h2 {
color: green;
margin-bottom: 1rem;
}
</style>