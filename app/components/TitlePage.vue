<template>
    <p class="text-3xl font-bold">{{ title }}</p>
</template>

<script setup>
const props = defineProps({
    title : {
        type : String,
        default : "Title Name"
    }
})
</script>