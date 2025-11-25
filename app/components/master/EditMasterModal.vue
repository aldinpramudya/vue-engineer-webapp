<template>
    <div v-if="show" class="z-50 fixed inset-0 bg-black/25 flex items-center justify-center">
        <div class="bg-white w-100 rounded-xl p-6 shadow-lg">
            <p class="text-xl font-bold mb-4">
                Edit Master Data
            </p>
            <!-- Form Data -->
            <div class="space-y-4">
                <div>
                    <label for="code">Code</label>
                    <input v-model="form.code" class="border rounded w-full p-2" placeholder="Code Name">
                </div>
                <div>
                    <label for="name">Name</label>
                    <input v-model="form.name" class="border rounded w-full p-2" placeholder="Name Data Master">
                </div>
                <div>
                    <label for="Category">Category COA</label>
                    <select v-model="form.category_coa_id" class="w-full border rounded-lg px-3 py-2 mb-4">
                        <option value="">-- Select Category COA --</option>
                        <option v-for="c in categories" :key="c.id" :value="c.id">
                            {{ c.name_category }}
                        </option>
                    </select>
                </div>
            </div>
            <div class="mt-4 flex justify-end gap-2">
                <button class="px-3 py-1 bg-gray-300 rounded" @click="close">
                    Cancel
                </button>
                <button class="px-3 py-1 bg-blue-600 text-white rounded" @click="handleEdit">
                    Save
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
// Sweet Alert 
import Swal from "sweetalert2"

const props = defineProps({
    show: Boolean,
    masterData: Object,
    categories: Array
});

const emit = defineEmits([
    "close",
    "saved",
]);

const close = () => {
    emit("close");
};

const form = ref({
    id: null,
    category_coa_id: "",
    code: "",
    name: "",
});

watch(
    () => props.masterData,
    (val) => {
        if (val) {
            form.value.id = val.id;
            form.value.category_coa_id = val.category_coa_id;
            form.value.code = val.code;
            form.value.name = val.name;
        }
    },
    { immediate: true }
);

const handleEdit = async () => {
    try {
        await $fetch(`http://127.0.0.1:8000/api/masters-coa/${form.value.id}`, {
            method: "PUT",
            body: {
                code: form.value.code,
                name: form.value.name,
                category_coa_id: form.value.category_coa_id,
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