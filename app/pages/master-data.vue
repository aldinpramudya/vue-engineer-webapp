<template>
    <div>
        <TitlePage title="Master Data Of Transaction" />
        <div class="pt-5">
            <!-- Button New Master Data -->
            <button @click="isModalOpen = true" class="px-4 py-2 font-bold rounded-lg bg-blue-500 text-white hover:bg-blue-600 transition">
                Add New Master Data
            </button>

            <MasterNewMasterModal :show="isModalOpen" @close="isModalOpen = false" @saved="fetchData"/>
            <!-- Button New Master Data -->
            <!-- Table -->
            <div class="pt-5">
                <div class="bg-white rounded-lg shadow-sm border border-gray-200 overflow-hidden">
                    <div class="px-6 py-4 border-b border-gray-200 bg-gray-50">
                        <p class="text-lg font-semibold text-gray-900">Table Master</p>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full">
                            <thead class="bg-gray-50 border-b border-gray-200">
                                <tr>
                                    <th v-for="column in columns" :key="column.key"
                                        class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                        {{ column.label }}
                                    </th>
                                    <th
                                        class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">
                                        Actions
                                    </th>
                                </tr>
                            </thead>
                            <tbody class="bg-white divide-y divide-gray-200">
                                <!-- If Data Loading -->
                                <tr v-if="pending" class="hover:bg-gray-50">
                                    <td :colspan="columns.length + 1" class="px-6 py-8 text-center text-gray-500">
                                        Loading...
                                    </td>
                                </tr>
                                <!-- If Data Loading End -->
                                <!-- If No Data -->
                                <tr v-else-if="!dataMasters.length" class="hover:bg-gray-50">
                                    <td :colspan="columns.length + 1" class="px-6 py-8 text-center text-gray-500">
                                        No data available
                                    </td>
                                </tr>
                                <!-- If No Data End -->
                                <!-- Data Fetch -->
                                <tr v-else v-for="item in dataMasters" :key="item.id"
                                    class="hover:bg-gray-50 transition-colors">
                                    <td v-for="column in columns" :key="column.key"
                                        class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                                        <slot :name="`cell-${column.key}`" :item="item">
                                            {{ item[column.key] }}
                                        </slot>
                                    </td>
                                    <!-- Buttons -->
                                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                                        <div class="flex items-center justify-end gap-2">
                                            <!-- Edit Button -->
                                            <button @click="$emit('edit', item)"
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
                                    <!-- Buttons End -->
                                </tr>
                                <!-- Data Fetch -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
            <!-- Table End -->
        </div>
    </div>
</template>

<script setup>

// Get Data Master
const { data: response, pending, error, refresh } = await useFetch("http://127.0.0.1:8000/api/masters-coa", {
    method: "GET",
});
const dataMasters = computed(() => {
    return (response.value?.data ?? []).map(item => ({
        ...item,
        name_category: item.category_coa.name_category
    }))
})
// Get Data Master End

// Handle Delete
const handleDelete = async (item) => {
    try {
        await $fetch(`http://127.0.0.1:8000/api/masters-coa/${item.id}`, {
            method: "DELETE",
        })
        console.log("Data Berhasil Dihapuskan")
        refresh();
    } catch (err) {
        console.log("Data Gagal Dihapuskan")
    }
}

// Handle Delete End

// Column Table
const columns = [
    { key: 'id', label: 'ID' },
    { key: 'code', label: 'Code' },
    { key: 'name', label: 'Name' },
    { key: 'name_category', label: 'Category' },
];
// Column Table End

// Toggle Create New Data Modal
const isModalOpen = ref(false);
// Toggle Create New Data End

const fetchData = async () => {
    await refresh()
    isModalOpen.value = false
}


</script>
