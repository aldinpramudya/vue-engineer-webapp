<template>
    <div>
        <TitlePage title="Categories Of Transaction" />
        <!-- Button Open Modal -->
        <div class="pt-5">
            <button @click="isModalOpen = true"
                class="px-4 py-2 font-bold rounded-lg bg-blue-500 text-white hover:bg-blue-600 transition">
                Add New Category
            </button>
        </div>

        <CategoryNewCategoryModal :show="isModalOpen" @close="isModalOpen = false" @saved="fetchData" />
        <!-- Button Open Modal End -->

        <div v-if="pending">
            Loading....
        </div>

        <div v-if="error" class="error">
            Terjadi kesalahan: {{ error.message }}
        </div>

        <div class="grid grid-cols-4 gap-4 pt-5" v-if="categories.length">
            <CategoriesCard v-for="item in categories" :key="item.id" :name="item.name_category"
                @delete="handleDelete(item)" @edit="handleEdit(item)" />
        </div>

        <CategoryEditCategoryModal :show="showEditModal" :category="selectedCategory" @close="showEditModal = false"
            @saved="fetchData" />
    </div>
</template>

<script setup>
import Swal from "sweetalert2"

// Fetch Get Data 
const { data: response, pending, error, refresh } = await useFetch("http://127.0.0.1:8000/api/categories-coa", {
    method: "GET",
});
const categories = computed(() => response.value?.data ?? [])
// Fetch Get Data End

// Handle Delete
const handleDelete = async (item) => {
    const result = await Swal.fire({
        title: "Hapus Data?",
        text: `Anda yakin ingin menghapus kategori "${item.name_category}"?`,
        icon: "warning",
        showCancelButton: true,
        confirmButtonText: "Ya, Hapus",
        cancelButtonText: "Batal",
    })

    if (!result.isConfirmed) {
        return
    }

    try {
        await $fetch(`http://127.0.0.1:8000/api/categories-coa/${item.id}`, {
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
const showEditModal = ref(false)
const selectedCategory = ref(null)

const handleEdit = (item) => {
    selectedCategory.value = { ...item }
    showEditModal.value = true
}

// Handle Edit End

// Toggle Create New Data Modal
const isModalOpen = ref(false);
// Toggle Create New Data End

const fetchData = async () => {
    await refresh()
    isModalOpen.value = false
}

</script>