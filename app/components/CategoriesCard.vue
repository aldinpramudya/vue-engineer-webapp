<template>
    <div
        class="bg-gray-200rounded-xl shadow-sm border border-gray-100 p-6 hover:shadow-md transition-shadow duration-200">
        <div class="flex flex-col justify-between">
            <div>
                <p class="text-2xl font-bold text-gray-900 mt-1">{{ name }}</p>
            </div>
            <!-- Button Edit and Delete -->
            <div class="flex gap-3 mt-4">
                <button @click="$emit('edit')"
                    class="px-4 py-2 rounded-lg bg-blue-500 text-white hover:bg-blue-600 transition">
                    Edit
                </button>

                <button @click="$emit('delete')"
                    class="px-4 py-2 rounded-lg bg-red-500 text-white hover:bg-red-600 transition">
                    Delete
                </button>

            </div>
            <!-- Button Edit and Delete End -->
        </div>
    </div>
</template>

<script setup>
const props = defineProps({
    name: {
        type: String,
        default: "Category Card"
    },
})
</script>