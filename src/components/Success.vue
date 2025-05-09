<script setup>
import { onMounted } from 'vue';

// Clear localStorage so user can't reuse token
onMounted(() => {
    localStorage.removeItem('access_token');
    localStorage.removeItem('customerId');
});

async function restartForm() {
    window.location.reload()
}
</script>

<template>
<div class="success-container">
    <h2>🎉 Success!</h2>
    <p>Your form has been successfully submitted. Thank you!</p>
    <p>If you need to make changes, please <strong>start over</strong>.</p>
    
    <!-- TODO -->
    <button @click="restartForm">Start New Form</button>
</div>
</template>

<style scoped>
.success-container {
text-align: center;
padding: 2rem;
max-width: 500px;
margin: auto;
}

h2 {
color: #28a745;
margin-bottom: 1rem;
}

p {
font-size: 1.1rem;
margin-bottom: 1rem;
}

button {
background-color: #007bff;
color: white;
border: none;
padding: 0.6rem 1.2rem;
font-size: 1rem;
border-radius: 5px;
cursor: pointer;
}
</style>