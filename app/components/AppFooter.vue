<template>
    <footer class="fixed bottom-0 left-0 right-0 bg-gray-200 text-gray-300">
        <div class="max-w-7xl mx-auto">
            <div class="py-5 text-center text-sm text-gray-500">
                <p>&copy; {{ currentYear }} TransactionApp. Code by Riel.</p>
            </div>
        </div>
    </footer>
</template>

<script setup>
const currentYear = new Date().getFullYear()
</script>
