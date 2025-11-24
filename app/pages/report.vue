<template>
    <div>
        <TitlePage title="Report Profit Page" />
        <!-- Form -->
        <div class="pt-5">
            <div class="border p-4 rounded mb-6">
                <label class="block mb-2 font-semibold">Pilih Bulan</label>
                <input type="number" v-model="month" min="1" max="12" class="border p-2 rounded mb-4 w-full" />

                <label class="block mb-2 font-semibold">Pilih Tahun</label>
                <input type="number" v-model="year" class="border p-2 rounded w-full mb-4" />

                <button @click="getMonthlyReport" class="bg-blue-600 text-white px-4 py-2 rounded">
                    Load Report
                </button>
            </div>

            <!-- Error -->
            <div v-if="errorMessage" class="bg-red-200 text-red-700 p-3 rounded mb-4">
                {{ errorMessage }}
            </div>

            <!-- Loading -->
            <div v-if="loading" class="text-gray-500">Loading...</div>

            <!-- Table -->
            <table v-if="report.length > 0" class="w-full border mt-4">
                <thead>
                    <tr class="bg-gray-200">
                        <th class="p-2 border">Category</th>
                        <th class="p-2 border">Total Debit</th>
                        <th class="p-2 border">Total Credit</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="item in report" :key="item.category_id">
                        <td class="p-2 border">{{ item.category_name }}</td>
                        <td class="p-2 border">{{ item.total_debit }}</td>
                        <td class="p-2 border">{{ item.total_credit }}</td>
                    </tr>
                </tbody>
            </table>

            <!-- Export -->
            <button v-if="report.length > 0" @click="exportExcel"
                class="mt-4 bg-green-600 text-white px-4 py-2 rounded">
                Export to Excel
            </button>
        </div>
    </div>
</template>

<script setup>
const month = ref('')
const year = ref('')

const report = ref([])
const loading = ref(false)
const errorMessage = ref('')

const getMonthlyReport = async () => {
    if (!month.value || !year.value) {
        errorMessage.value = "Bulan dan tahun wajib diisi."
        return
    }

    loading.value = true
    errorMessage.value = ''

    const { data, error } = await useFetch(
        'http://localhost:8000/api/get-laporan-profit-loss',
        {
            method: 'GET',
            params: {
                month: month.value,
                year: year.value
            }
        }
    )

    if (error.value) {
        errorMessage.value = "Gagal mengambil data"
    } else {
        report.value = data.value.data
    }

    loading.value = false
}

// export excel
const exportExcel = () => {
    if (!month.value || !year.value) return

    window.location.href =
        `http://localhost:8000/api/excel-report-export?month=${month.value}&year=${year.value}`
}

</script>
