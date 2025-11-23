<template>
    <div v-if="show" class="z-50 fixed inset-0 bg-black/25 flex items-center justify-center">
        <div class="bg-white w-100 rounded-xl p-6 shadow-lg">
            <p class="text-xl font-bold mb-4">
                Add New Master Data
            </p>
            <!-- Form Start -->
            <form @submit.prevent="submitForm">
                <label class="block mb-2 font-medium">
                    New Data Master Name
                </label>
                <!-- Input Data Master-->
                <div class="space-y-4">
                    <div>
                        <label for="code">Code</label>
                        <input v-model="code" type="text" class="w-full border rounded-lg px-3 py-2 mb-4"
                            placeholder="Enter Your New Code Number (Etc: 453)">
                    </div>
                    <div>
                        <label for="name">Name</label>
                        <input v-model="name" type="text" class="w-full border rounded-lg px-3 py-2 mb-4"
                            placeholder="Enter Data Master Name">
                    </div>
                    <div>
                        <label for="Category">Category COA</label>
                        <select v-model="category_coa_id" class="w-full border rounded-lg px-3 py-2 mb-4">
                            <option value="">Select Category</option>
                            <option v-for="cat in categories" :key="cat.id" :value="cat.id">
                                {{ cat.name_category }}
                            </option>
                        </select>

                    </div>
                </div>
                <div class="mt-4 flex justify-end gap-2">
                    <button type="button" @click="handleClose" class="px-3 py-1 bg-gray-300 rounded">
                        Cancel
                    </button>

                    <button type="submit" class="px-3 py-1 bg-blue-600 text-white rounded">
                        Save
                    </button>
                </div>
            </form>
            <!-- Form End -->
        </div>
    </div>
</template>

<script setup>
const props = defineProps({
    show: Boolean,
});

const emit = defineEmits([
    "close",
    "saved"
]);

// Input State
const code = ref("");
const name = ref("");
const category_coa_id = ref("");
// Category List
const categories = ref([]);

watch(
    () => props.show,
    async (val) => {
        if (val) {
            await loadCategories();
        }
    }
);

const handleClose = () => {
    code.value = "",
    name.value = "",
    category_coa_id.value = "",
    emit("close");
}

// Load Pilihan Category
const loadCategories = async () => {
    const res = await $fetch("http://127.0.0.1:8000/api/categories-coa");
    categories.value = res.data; 
}
// Load Pilihan Category End

// Input New Data Master
const submitForm = async () => {
    try {
        await $fetch("http://127.0.0.1:8000/api/masters-coa", {
            method: "POST",
            body: {
                code: code.value,
                name: name.value,
                category_coa_id: category_coa_id.value
            }
        });
        emit("saved");
        handleClose();
    } catch (err) {
        console.log("Data Gagal Diinputkan")
    }
}
// Input New Data Master End
</script>