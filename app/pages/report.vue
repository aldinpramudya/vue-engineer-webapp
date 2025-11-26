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

                <button @click="loadReport" class="bg-blue-600 text-white px-4 py-2 rounded">
                    Load Report
                </button>
            </div>

            <!-- Error -->
            <div v-if="errorMessage" class="bg-red-200 text-red-700 p-3 rounded mb-4">
                {{ errorMessage }}
            </div>

            <!-- Loading -->
            <div v-if="loading" class="text-gray-500">Loading...</div>

            <!-- TABLE -->
            <div v-if="income.length || expenses.length">
                <table class="w-full border">
                    <thead>
                        <tr class="bg-gray-200">
                            <th class="p-2 border">Category</th>
                            <th class="p-2 border">Amount</th>
                        </tr>
                    </thead>

                    <tbody>
                        <!-- INCOME SECTION -->
                        <tr class="bg-green-200 font-bold">
                            <td class="p-2 border" colspan="2">INCOME</td>
                        </tr>

                        <tr v-for="item in income" :key="item.category_id" class="bg-green-50">
                            <td class="p-2 border">{{ item.category_name }}</td>
                            <td class="p-2 border text-right">
                                {{ formatAmount(item.total_credit - item.total_debit) }}
                            </td>
                        </tr>

                        <!-- Total Income -->
                        <tr class="bg-green-300 font-bold">
                            <td class="p-2 border">Total Income</td>
                            <td class="p-2 border text-right">{{ totalIncomeFormatted }}</td>
                        </tr>

                        <!-- EXPENSES SECTION -->
                        <tr class="bg-red-200 font-bold">
                            <td class="p-2 border" colspan="2">EXPENSES</td>
                        </tr>

                        <tr v-for="item in expenses" :key="item.category_id" class="bg-red-50">
                            <td class="p-2 border">{{ item.category_name }}</td>
                            <td class="p-2 border text-right">
                                -{{ formatAmount(item.total_debit - item.total_credit) }}
                            </td>
                        </tr>

                        <!-- Total Expenses -->
                        <tr class="bg-red-300 font-bold">
                            <td class="p-2 border">Total Expenses</td>
                            <td class="p-2 border text-right">-{{ totalExpensesFormatted }}</td>
                        </tr>

                        <!-- NET INCOME -->
                        <tr class="bg-yellow-200 font-bold">
                            <td class="p-2 border text-center">Net Income</td>
                            <td class="p-2 border text-right">{{ formatAmount(netIncome) }}</td>
                        </tr>
                    </tbody>
                </table>

                <!-- Export -->
                <button @click="exportExcel" class="mt-4 bg-green-600 text-white px-4 py-2 rounded">
                    Export to Excel
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
const month = ref('')
const year = ref('')
const loading = ref(false)
const errorMessage = ref('')

const income = ref([])
const expenses = ref([])

const totalAllDebit = ref(0)
const totalAllCredit = ref(0)
const netIncome = ref(0)

// Format angka
const formatAmount = (val) =>
    Number(val).toLocaleString("id-ID", { minimumFractionDigits: 0 })

// Total per kategori
const totalIncomeFormatted = computed(() =>
    formatAmount(totalAllCredit.value)
)

const totalExpensesFormatted = computed(() =>
    formatAmount(totalAllDebit.value)
)

// LOAD REPORT
const loadReport = async () => {
    if (!month.value || !year.value) {
        errorMessage.value = "Bulan dan tahun wajib diisi."
        return
    }

    loading.value = true
    errorMessage.value = ""

    try {
        const res = await $fetch("http://127.0.0.1:8000/api/get-laporan-profit-loss", {
            method: "GET",
            query: {
                month: month.value,
                year: year.value
            }
        })

        totalAllDebit.value = res.total_all_debit
        totalAllCredit.value = res.total_all_credit
        netIncome.value = res.net_income

        income.value = res.data.filter(i => Number(i.total_credit) > 0)
        expenses.value = res.data.filter(i => Number(i.total_debit) > 0)

    } catch (err) {
        errorMessage.value = "Gagal mengambil data"
        console.error(err)
    }

    loading.value = false
}

// EXPORT
const exportExcel = () => {
    if (!month.value || !year.value) {
        errorMessage.value = "Bulan dan tahun wajib diisi untuk export."
        return
    }

    window.open(
        `http://127.0.0.1:8000/api/excel-report-export?month=${month.value}&year=${year.value}`,
        "_blank"
    )
}

</script>
