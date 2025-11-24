<template>
    <div v-if="show" class="z-50 fixed inset-0 bg-black/25 flex items-center justify-center">
        <div class="bg-white w-100 rounded-xl p-6 shadow-lg">
            <p class="text-xl font-bold mb-4">
                Edit Transaction Data
            </p>
            <!-- Form Data -->
            <div class="space-y-4">
                <div>
                    <label for="masters_coa_id">Master COA Code</label>
                    <select v-model="form.masters_coa_id" class="w-full border rounded-lg px-3 py-2 mb-4">
                        <option value="">-- Select Category COA --</option>
                        <option v-for="data in masterData" :key="data.id" :value="data.id">
                            {{ data.code }} - {{ data.name }}
                        </option>
                    </select>
                </div>
                <div>
                    <label for="description">Description</label>
                    <input v-model="form.description" type="text" class="w-full border rounded-lg px-3 py-2 mb-4"
                        placeholder="Description of Your Transaction">
                </div>
                <div>
                    <label for="debit">Debit</label>
                    <input v-model="form.debit" type="number" class="w-full border rounded-lg px-3 py-2 mb-4"
                        placeholder="Your Debit on transaction">
                </div>
                <div>
                    <label for="credit">Credit</label>
                    <input v-model="form.credit" type="number" class="w-full border rounded-lg px-3 py-2 mb-4"
                        placeholder="Your Credit on transaction">
                </div>
            </div>
            <!-- Form Data End -->
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
const props = defineProps({
    show: Boolean,
    transactionData: Object,
    masterData: Array,
});

const emit = defineEmits([
    "close",
    "saved",
]);

const close = () => {
    emit("close")
};

const form = ref({
    id: null,
    masters_coa_id: "",
    description: "",
    debit: 0,
    credit: 0,
});

watch(
    () => props.transactionData,
    (val) => {
        if (val) {
            form.value.id = val.id;
            form.value.masters_coa_id = val.masters_coa_id
            form.value.description = val.description;
            form.value.debit = val.debit;
            form.value.credit = val.credit;
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
                debit: form.value.debit,
                credit: form.value.credit,
            }
        });
        emit("saved");
        emit("close");
    } catch (err) {
        console.log(err);
    }
}
</script>