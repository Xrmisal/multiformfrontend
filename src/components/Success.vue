<script setup>

async function restartForm() {
    window.location.reload()
}
</script>

<template>
    <div class="min-h-screen bg-gray-900 text-white flex items-center justify-center px-4">
    <div class="bg-gray-800 text-center p-10 rounded-2xl shadow-xl max-w-md w-full space-y-6">
        <h2 class="text-3xl font-bold text-teal-400">🎉 Success!</h2>
        <p class="text-gray-300">
        Your form has been successfully submitted. Thank you!
        </p>
        <p class="text-gray-400 text-sm">
        If you need to make changes, please <strong>start over</strong>.
        </p>

        <button
        @click="restartForm"
        class="bg-teal-500 hover:bg-teal-600 text-white font-semibold py-2 px-6 rounded-md transition">
        Start New Form
        </button>
    </div>
    </div>
</template>


<style scoped>

</style>