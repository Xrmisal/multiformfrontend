<script setup>
const emit = defineEmits(['complete'])

function goToComplete() {
  // For now, simulate completion
    emit('complete', {
        step: 3,
        message: 'Form completed!'
    })
}
</script>

<template>
    <div>
    <h2>Step 2: Final Step</h2>
    <p>This is just a placeholder. Imagine DOB and Income go here.</p>

    <button @click="goToComplete">Finish</button>
    </div>
</template>

<style scoped>
    h2 {
    margin-bottom: 0.5rem;
    }
    button {
    padding: 0.6rem 1rem;
    font-size: 1rem;
    background-color: #35495e;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    }
</style>