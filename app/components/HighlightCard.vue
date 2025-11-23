<template>
    <div class="bg-gray-200 w-sm rounded-xl shadow-sm border border-gray-100 p-6 hover:shadow-md transition-shadow duration-200">
        <div class="flex items-center justify-between">
            <div>
                <p class="text-sm font-medium text-gray-500">{{ title }}</p>
                <p class="text-2xl font-bold text-gray-900 mt-1">{{ formatedValue }}</p>
            </div>
        </div>
    </div>
</template>

<script setup>
import {computed} from 'vue';

const props = defineProps({
    title : {
        type : String,
        default : 'Card Title'
    },
    value : {
        type : [Number, String],
        default : 0
    }
});

const formatedValue = computed(() => {
    if (typeof props.value === 'number'){
        return props.value.toLocaleString()
    }
    return props.value
});

</script>