<template>
    <div v-if="show" class="z-50 fixed inset-0 bg-black/25 flex items-center justify-center">
        <div class="bg-white w-100 rounded-xl p-6 shadow-lg">
            <p class="text-xl font-bold mb-4">
                Add New Transaction
            </p>
            <!-- Form Start -->
            <form @submit.prevent="submitForm">
                <label class="block mb-2 font-medium">
                    New Data Transaction
                </label>
                <!-- Input Data Transaction -->
                <div class="space-y-4">
                    <div>
                        <label for="masters_coa_id">Master COA Code</label>
                        <select v-model="masters_coa_id" class="w-full border rounded-lg px-3 py-2 mb-4">
                            <option value="">Select Data Master COA</option>
                            <option v-for="data in mastersData" :key="data.id" :value="data.id">
                                {{ data.code }} - {{ data.name }}
                            </option>
                        </select>
                    </div>
                    <div>
                        <label for="description">Description</label>
                        <input v-model="description" type="text" class="w-full border rounded-lg px-3 py-2 mb-4"
                            placeholder="Enter Description of Your Transaction">
                    </div>
                    <div>
                        <label for="amout">Amount</label>
                        <input v-model="amount" type="number" class="w-full border rounded-lg px-3 py-2 mb-4"
                            placeholder="Enter Amount of Your Transaction">
                    </div>
                </div>
                <!-- Input Data Transaction End -->
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
// Sweet Alert 
import Swal from "sweetalert2"

const props = defineProps({
    show: Boolean,
});

const emit = defineEmits([
    "close",
    "saved",
]);

// Input State
const masters_coa_id = ref("");
const description = ref("");
const amount = ref(0);

const handleClose = () => {
    masters_coa_id.value = "",
        description.value = "",
        amount.value = 0;
    emit("close");
}

// Load Pilihan Master Data COA
const mastersData = ref([]);

watch(
    () => props.show,
    async (val) => {
        if (val) {
            await loadMastersData();
        }
    }
)

const loadMastersData = async () => {
    const res = await $fetch("http://127.0.0.1:8000/api/masters-coa");
    mastersData.value = res.data;
}
// Load Pilihan Master Data COA Endw

// Submit Form Handler
const submitForm = async () => {
    if (!masters_coa_id.value || !description.value || !amount.value) {
        Swal.fire("Oops!", "Semua field wajib diisi", "warning");
        return;
    }

    try {
        await $fetch("http://127.0.0.1:8000/api/transactions", {
            method: "POST",
            body: {
                masters_coa_id: masters_coa_id.value,
                description: description.value,
                amount: amount.value,
            }
        });

        Swal.fire({
            title: "Berhasil!",
            text: "Data baru berhasil disimpan.",
            icon: "success",
            timer: 1500,
            showConfirmButton: true,
        });

        emit("saved");
        handleClose();
    } catch (error) {
        console.log("Data Gagal Diinputkan")
    }
}
// Submit Form Handler End

</script>