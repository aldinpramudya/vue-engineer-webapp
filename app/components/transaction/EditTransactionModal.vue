<template>
    <div v-if="show" class="z-50 fixed inset-0 bg-black/25 flex items-center justify-center">
        <div class="bg-white w-100 rounded-xl p-6 shadow-lg">
            <p class="text-xl font-bold mb-4">Edit Transaction Data</p>

            <div class="space-y-4">
                <!-- Master COA -->
                <div>
                    <label for="masters_coa_id">Master COA Code</label>
                    <select v-model="form.masters_coa_id" class="w-full border rounded-lg px-3 py-2 mb-4">
                        <option value="">-- Select Category COA --</option>
                        <option v-for="data in masterData" :key="data.id" :value="data.id">
                            {{ data.code }} - {{ data.name }}
                        </option>
                    </select>
                </div>

                <!-- Date -->
                <div>
                    <label for="date">Transaction Date</label>
                    <input v-model="form.date" type="date" class="w-full border rounded-lg px-3 py-2 mb-4" />
                </div>

                <!-- Description -->
                <div>
                    <label for="description">Description</label>
                    <input v-model="form.description" type="text" class="w-full border rounded-lg px-3 py-2 mb-4"
                        placeholder="Description of Your Transaction" />
                </div>

                <!-- Amount -->
                <div>
                    <label for="amount">Amount</label>
                    <input v-model="form.amount" type="number" class="w-full border rounded-lg px-3 py-2 mb-4"
                        placeholder="Transaction amount" />
                </div>
            </div>

            <!-- Button -->
            <div class="mt-4 flex justify-end gap-2">
                <button class="px-3 py-1 bg-gray-300 rounded" @click="close">Cancel</button>
                <button class="px-3 py-1 bg-blue-600 text-white rounded" @click="handleEdit">
                    Save
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
import Swal from "sweetalert2";

const props = defineProps({
    show: Boolean,
    transactionData: Object,
    masterData: Array,
});

const emit = defineEmits(["close", "saved"]);

const close = () => emit("close");

const form = ref({
    id: null,
    masters_coa_id: "",
    description: "",
    amount: 0,
    date: "",
});

watch(
    () => props.transactionData,
    (val) => {
        if (val) {
            form.value.id = val.id;
            form.value.masters_coa_id = val.masters_coa_id;
            form.value.description = val.description;
            form.value.amount = val.debit > 0 ? val.debit : val.credit;
            form.value.date = val.date ? val.date.substring(0, 10) : "";
        }
    },
    { immediate: true }
);

const handleEdit = async () => {
    try {
        await $fetch(`http://127.0.0.1:8000/api/transactions/${form.value.id}`, {
            method: "PUT",
            body: {
                masters_coa_id: form.value.masters_coa_id,
                description: form.value.description,
                amount: Number(form.value.amount),
                date: form.value.date,
            },
        });

        Swal.fire({
            title: "Berhasil!",
            text: "Data berhasil diperbarui.",
            icon: "success",
            timer: 1500,
            showConfirmButton: false,
        });

        emit("saved");
        emit("close");
    } catch (err) {
        console.log(err);
    }
};
</script>
