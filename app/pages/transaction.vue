<template>
    <div>
        <TitlePage title="Transaction Page" />
        <div class="pt-5">
            <!-- button Open Modal -->
            <button @click="isModalOpen = true"
                class="px-4 py-2 font-bold rounded-lg bg-blue-500 text-white hover:bg-blue-600 transition">
                Add New Data Transaction
            </button>

            <TransactionNewTransactionModal :show="isModalOpen" @close="isModalOpen = false" @saved="fetchData" />
            <!-- Button Open Modal End -->

            <!-- Table -->
            <div class="pt-5">
                <div class="bg-white rounded-lg shadow-sm border border-gray-200 overflow-hidden">
                    <div class="px-6 py-4 border-b border-gray-200 bg-gray-50">
                        <p class="text-lg font-semibold text-gray-900">Transaction History</p>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full">
                            <thead class="bg-gray-50 border-b border-gray-200">
                                <!-- Head Columns -->
                                <tr>
                                    <th v-for="column in columns" :key="column.key"
                                        class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                        {{ column.label }}
                                    </th>
                                    <th
                                        class="px-6 py-3 text-center text-xs font-medium text-gray-500 uppercase tracking-wider">
                                        Actions
                                    </th>
                                </tr>
                                <!-- Head Columns End -->
                            </thead>
                            <tbody class="bg-white divide-y divide-gray-200">
                                <!-- If Data Lading -->
                                <tr v-if="pending" class="hover:bg-gray-50">
                                    <td :colspan="columns.length + 1" class="px-6 py-8 text-center text-gray-500">
                                        Loading...
                                    </td>
                                </tr>
                                <!-- If Data Lading End -->
                                <!-- If No Data -->
                                <tr v-else-if="!dataTransactions.length" class="hover:bg-gray-50">
                                    <td :colspan="columns.length + 1" class="px-6 py-8 text-center text-gray-500">
                                        No data available
                                    </td>
                                </tr>
                                <!-- If No Data End -->
                                <!-- Data Fetch -->
                                <!-- Buttons -->
                                <tr v-else v-for="item in dataTransactions" :key="item.id"
                                    class="hover:bg-gray-50 transition colors">
                                    <td v-for="column in columns" :key="column.key"
                                        class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                                        <slot :name="`cell-${column.key}`" :item="item">
                                            {{ item[column.key] }}
                                        </slot>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap text-center text-sm font-medium">
                                        <div class="flex items-center justify-center gap-2">
                                            <!-- Edit Button -->
                                            <button @click="handleEdit(item)"
                                                class="inline-flex items-center gap-1 px-3 py-1.5 text-blue-600 hover:text-blue-700 hover:bg-blue-50 rounded-md transition-colors"
                                                title="Edit">
                                                <svg class="w-4 h-4" fill="none" stroke="currentColor"
                                                    viewBox="0 0 24 24">
                                                    <path stroke-linecap="round" stroke-linejoin="round"
                                                        stroke-width="2"
                                                        d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                                                </svg>
                                                <span>Edit</span>
                                            </button>

                                            <!-- Delete Button -->
                                            <button @click="handleDelete(item)"
                                                class="inline-flex items-center gap-1 px-3 py-1.5 text-red-600 hover:text-red-700 hover:bg-red-50 rounded-md transition-colors"
                                                title="Delete">
                                                <svg class="w-4 h-4" fill="none" stroke="currentColor"
                                                    viewBox="0 0 24 24">
                                                    <path stroke-linecap="round" stroke-linejoin="round"
                                                        stroke-width="2"
                                                        d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                                                </svg>
                                                <span>Delete</span>
                                            </button>
                                        </div>
                                    </td>
                                </tr>
                                <!-- Buttons End -->
                                <!-- Data Fetch End -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
            <!-- Table End -->
            <TransactionEditTransactionModal :show="showEditModal" :transaction-data="selectedTransactionData"
                :master-data="dataMasters" @close="showEditModal = false" @saved="fetchData" />
        </div>
    </div>
</template>

<script setup>
import Swal from "sweetalert2"

// Get Data Transaction
const { data: response, pending, error, refresh } = await useFetch("http://127.0.0.1:8000/api/transactions", {
    method: "GET",
});
const dataTransactions = computed(() => {
    return (response.value?.data ?? []).map(item => ({
        ...item,
        code: item.master_coa.code,
        name: item.master_coa.name,
        type_category : item.master_coa.category_coa.type_category,
    }));
});
// Get Data Transaction End

// Handle Delete
const handleDelete = async (item) => {
    const result = await Swal.fire({
        title: "Hapus Data?",
        text: `Anda yakin ingin menghapus Transaksi ini ?`,
        icon: "warning",
        showCancelButton: true,
        confirmButtonText: "Ya, Hapus",
        cancelButtonText: "Batal",
    })

    if (!result.isConfirmed) {
        return
    }

    try {
        await $fetch(`http://127.0.0.1:8000/api/transactions/${item.id}`, {
            method: "DELETE",
        })

         Swal.fire({
            title: "Berhasil!",
            text: "Data berhasil dihapus.",
            icon: "success",
            timer: 1500,
            showConfirmButton: true,
        })

        console.log("Data Berhasil Dihapuskan")
        refresh();
    } catch (err) {
        console.log("Data Gagal Dihapuskan")
    }
}
// Handle Delete End

// Handle Edit
const showEditModal = ref(false);
const selectedTransactionData = ref(null);

const dataMasters = ref([]);

const fetchMasters = async () => {
    try {
        const res = await $fetch("http://127.0.0.1:8000/api/masters-coa")
        dataMasters.value = res.data
    } catch (error) {
        console.error("Failed To Fetch Data Masters:", error)
    }
}

onMounted(() => {
    fetchMasters();
});

const handleEdit = (item) => {
    selectedTransactionData.value = { ...item }
    showEditModal.value = true;
}
// Handle Edit End

// Column Table
const columns = [
    { key: 'id', label: 'ID' },
    { key: 'date', label: 'Date' },
    { key: 'code', label: 'Master Code' },
    { key: 'name', label: 'Master Data Name' },
    { key: 'description', label: 'Description Transaction' },
    { key: 'type_category', label: 'Type Category' },
    { key: 'debit', label: 'Debit' },
    { key: 'credit', label: 'Credit' }
]
// Column Table End

// Toggle Create New Data Modal
const isModalOpen = ref(false);
// Toggle Create New Data End

const fetchData = async () => {
    await refresh()
    isModalOpen.value = false
}
</script>