<template>
    <div>
        <TitlePage title="Yearly Report" />

        <div class="pt-5">
            <div class="border p-4 rounded mb-6">
                <label class="block mb-2 font-semibold">Pilih Tahun</label>
                <input type="number" v-model="year" class="border p-2 rounded mb-4 w-full" />

                <button @click="loadReportYear" class="bg-blue-600 text-white px-4 py-2 rounded">
                    Load Yearly Report
                </button>
            </div>

            <div v-if="errorMessage" class="bg-red-200 text-red-700 p-3 rounded mb-4">
                {{ errorMessage }}
            </div>

            <div v-if="loading" class="text-gray-500">Loading...</div>

            <!-- ========================= -->
            <!-- DISPLAY YEARLY REPORT     -->
            <!-- ========================= -->
            <div v-for="item in yearlyData" :key="item.month" class="mb-12">

                <h2 class="text-xl font-bold mb-3">
                    {{ item.month_name }}
                </h2>

                <table class="w-full border">
                    <thead>
                        <tr class="bg-gray-200">
                            <th class="p-2 border">Category</th>
                            <th class="p-2 border">Amount</th>
                        </tr>
                    </thead>

                    <tbody>
                        <!-- INCOME -->
                        <tr class="bg-green-200 font-bold">
                            <td class="p-2 border" colspan="2">INCOME</td>
                        </tr>

                        <tr v-for="i in item.income" :key="'inc-' + i.category_id" class="bg-green-50">
                            <td class="p-2 border">{{ i.category_name }}</td>
                            <td class="p-2 border text-right">
                                {{ formatAmount(i.total_credit - i.total_debit) }}
                            </td>
                        </tr>

                        <tr class="bg-green-300 font-bold">
                            <td class="p-2 border">Total Income</td>
                            <td class="p-2 border text-right">
                                {{ formatAmount(item.total_credit) }}
                            </td>
                        </tr>

                        <!-- EXPENSES -->
                        <tr class="bg-red-200 font-bold">
                            <td class="p-2 border" colspan="2">EXPENSES</td>
                        </tr>

                        <tr v-for="e in item.expenses" :key="'exp-' + e.category_id" class="bg-red-50">
                            <td class="p-2 border">{{ e.category_name }}</td>
                            <td class="p-2 border text-right">
                                -{{ formatAmount(e.total_debit - e.total_credit) }}
                            </td>
                        </tr>

                        <tr class="bg-red-300 font-bold">
                            <td class="p-2 border">Total Expenses</td>
                            <td class="p-2 border text-right">
                                -{{ formatAmount(item.total_debit) }}
                            </td>
                        </tr>

                        <!-- NET INCOME -->
                        <tr class="bg-yellow-200 font-bold">
                            <td class="p-2 border text-center">Net Income</td>
                            <td class="p-2 border text-right">
                                {{ formatAmount(item.net_income) }}
                            </td>
                        </tr>
                    </tbody>
                </table>

            </div>

        </div>
    </div>
</template>

<script setup>
const year = ref("");
const loading = ref(false);
const errorMessage = ref("");

const yearlyData = ref([]);

// Format angka
const formatAmount = (val) =>
    Number(val).toLocaleString("id-ID", { minimumFractionDigits: 0 });

// LOAD YEARLY REPORT
const loadReportYear = async () => {
    if (!year.value) {
        errorMessage.value = "Masukkan Tahun terlebih dahulu";
        return;
    }

    loading.value = true;
    errorMessage.value = "";
    yearlyData.value = [];

    try {
        const res = await $fetch("http://127.0.0.1:8000/api/get-year-laporan-profit-loss", {
            method: "GET",
            query: { year: year.value },
        });

        // Transformasi per bulan: pisahkan income/expenses
        yearlyData.value = res.data.map((m) => {
            return {
                ...m,
                income: m.categories.filter((c) => Number(c.total_credit) > 0),
                expenses: m.categories.filter((c) => Number(c.total_debit) > 0),
            };
        });

    } catch (err) {
        errorMessage.value = "Gagal memuat laporan tahunan.";
        console.error(err);
    }

    loading.value = false;
};
</script>
