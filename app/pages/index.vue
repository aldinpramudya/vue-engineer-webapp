<template>
    <div>
        <TitlePage title="Dashboard" />
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mt-10">
            <HighlightCard title="Total Categories" :value="categoriesCount" />
            <HighlightCard title="Total Master COA" :value="masterCoaCount" />
            <HighlightCard title="Total Transactions" :value="transactionsCount" />
        </div>
    </div>
</template>

<script setup>
const { data: categoryRes } = await useFetch("http://127.0.0.1:8000/api/categories-coa");
const categoriesCount = computed(() => categoryRes.value?.data?.length ?? 0);

// Master COA
const { data: masterRes } = await useFetch("http://127.0.0.1:8000/api/masters-coa");
const masterCoaCount = computed(() => masterRes.value?.data?.length ?? 0);

// Transactions
const { data: transactionRes } = await useFetch("http://127.0.0.1:8000/api/transactions");
const transactionsCount = computed(() => transactionRes.value?.data?.length ?? 0);

</script>