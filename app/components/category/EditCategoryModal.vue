<template>
    <div v-if="show" class="z-50 fixed inset-0 bg-black/25 flex items-center justify-center">
        <div class="bg-white w-96 rounded-xl p-6 shadow-lg">
            <p class="text-xl font-bold mb-4">
                Edit Category
            </p>
            <!-- Edit Form -->
            <input v-model="form.name_category" class="border rounded w-full p-2" placeholder="Category Name" />
            <div class="pt-4">
                <select v-model="form.type_category" class="border rounded w-full p-2">
                    <option v-for="value in type_category_data" :key="value.id" :value="value.value">
                        {{ value.text }}
                    </option>
                </select>
            </div>
            <!-- Edit Form End -->
            <!-- Buttons Start-->
            <div class="mt-4 flex justify-end gap-2">
                <button class="px-3 py-1 bg-gray-300 rounded" @click="close">
                    Cancel
                </button>
                <button class="px-3 py-1 bg-blue-600 text-white rounded" @click="handleEdit">
                    Save
                </button>
            </div>
            <!-- Buttons End -->
        </div>
    </div>
</template>

<script setup>
// Sweet Alert 
import Swal from "sweetalert2"

const props = defineProps({
    show: Boolean,
    category: Object
});

const emit = defineEmits([
    "close",
    "saved",
]);

const close = () => {
    emit("close");
};

const type_category_data = [
    { id: 1, value: "Expenses", text: "Expenses" },
    { id: 2, value: "Income", text: "Income" }
]

const form = ref({
    id: null,
    name_category: "",
    type_category: "",
});

watch(
    () => props.category,
    (val) => {
        if (val) {
            form.value.id = val.id;
            form.value.name_category = val.name_category;
            form.value.type_category = val.type_category;
        }
    },
    { immediate: true }
);

const handleEdit = async () => {
    try {
        await $fetch(`http://127.0.0.1:8000/api/categories-coa/${form.value.id}`, {
            method: "PUT",
            body: {
                name_category: form.value.name_category,
                type_category: form.value.type_category
            }
        });

        Swal.fire({
            title: "Berhasil!",
            text: "Data berhasil Diedit.",
            icon: "success",
            timer: 1500,
            showConfirmButton: true,
        })


        emit("saved");
        emit("close");

    } catch (err) {
        console.log(err);
    }
}

</script>