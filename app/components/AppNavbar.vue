<template>
    <nav class="bg-white shadow-md sticky top-0 z-50">
        <div class="mx-40">
            <div class="flex items-center space-x-10 h-20">
                <!-- Home -->
                <NuxtLink to="/">
                    <span class="text-2xl font-bold text-(--main-color)">TransactionApp</span>
                </NuxtLink>
                <!-- Home End -->
                <!-- Dekstop Menu -->
                <div class="hidden md:flex items-center gap-8">
                    <ul class="flex items-center gap-1">
                        <li v-for="link in links" :key="link.id">
                            <NuxtLink :to="link.path"
                                class="px-4 py-2 rounded-md text-gray-600 font-medium hover:text-(--main-color) hover:bg-blue-50 transition-colors duration-200"
                                active-class="text-blue-600 bg-blue-50">
                                {{ link.name }}
                            </NuxtLink>
                        </li>
                    </ul>
                </div>
                <!-- Dekstop Menu End -->
                <!-- Mobile View -->


                <!-- Mobile View End -->
            </div>
        </div>
    </nav>
</template>

<script setup>
import { ref } from 'vue';

const isOpen = ref(false)

const links = [
    { id: 1, name: 'Home', path: '/' },
    { id: 2, name: 'Categories Of Coa', path: '/categories' },
    { id: 3, name: 'Master Data Coa', path: '/master-data' },
    { id: 4, name: 'Transaction', path: '/transaction' },
    { id: 5, name: 'Report Profit/Loss', path: '/report' },
]
</script>