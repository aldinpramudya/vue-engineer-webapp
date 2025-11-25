<template>
    <div v-if="show" class="z-50 fixed inset-0 bg-black/25 flex items-center justify-center">
        <div class="bg-white w-96 rounded-xl p-6 shadow-lg">
            <p class="text-xl font-bold mb-4">
                Add New Category
            </p>
            <!-- Form Start -->
            <form @submit.prevent="submitForm">
                <label class="block mb-2 font-medium">
                    New Category Name
                </label>
                <!-- Input New Category Starts -->
                <input v-model="name_category" type="text" class="w-full border rounded-lg px-3 py-2 mb-4"
                    placeholder="Enter Your New Category Name">
                <!-- Input New Category End -->
                <!-- Input Type Category -->
                <select v-model="type_category" class="w-full border rounded-lg px-3 py-2 mb-4"
                    placeholder="Pick Your Type Category">
                    <option value="">Select Category </option>
                    <option v-for="value in type_category_data" :key="value.id" :value="value.value">
                        {{ value.text }}
                    </option>
                </select>

                <!-- Input Type Category End -->
                <!-- Action Button -->
                <div class="flex justify-end gap-3 mt-4">
                    <button type="button" @click="handleClose"
                        class="px-4 py-2 rounded-lg bg-gray-300 hover:bg-gray-400">
                        Cancel
                    </button>
                    <button type="submit" class="px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-blue-700">
                        Save
                    </button>
                </div>
                <!-- Action Button End -->
            </form>
            <!-- Form End -->
        </div>
    </div>
</template>

<script setup>
// Sweet Alert 
import Swal from "sweetalert2"

const props = defineProps({
    show: Boolean,
})

const emit = defineEmits([
    "close",
    "saved"
]);

const handleClose = () => {
    name_category.value = ""
    type_category.value = "";
    emit("close")
}

// Input New Category
const name_category = ref("")
const type_category = ref("");

const type_category_data = [
    { id: 1 ,value: "Expenses", text: "Expenses" },
    { id: 2 ,value: "Income", text: "Income" }
]

const submitForm = async () => {
    if (!name_category.value | !type_category.value) {
        Swal.fire("Oops!", "Seluruh Data Wajib Diisi", "warning")
        return
    }

    try {
        const response = await $fetch("http://127.0.0.1:8000/api/categories-coa", {
            method: "POST",
            body: {
                name_category: name_category.value,
                type_category: type_category.value,
            }
        })

        Swal.fire({
            title: "Berhasil!",
            text: "Data baru berhasil disimpan.",
            icon: "success",
            timer: 1500,
            showConfirmButton: true,
        })

        console.log("Data Berhasil diinputkan")

        emit("saved")
        handleClose();
    } catch (err) {
        console.log("Data Gagal diinputkan")
    }
}

// Input New Category End

</script>