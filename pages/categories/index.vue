<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'

interface Category {
  id: number
  name: string
}

// State untuk kategori
const categories = ref<Category[]>([])
const showModal = ref(false)
const editingCategory = ref<Category | null>(null)
const form = reactive({
  id: null as number | null,
  name: '',
})

// Fungsi untuk mengambil data kategori dari local storage
const fetchCategories = () => {
  const storedCategories = localStorage.getItem('categories')
  if (storedCategories) {
    categories.value = JSON.parse(storedCategories)
  }
}

// Fungsi untuk menyimpan data kategori ke local storage
const saveCategories = () => {
  localStorage.setItem('categories', JSON.stringify(categories.value))
}

// Mengambil data kategori dari local storage saat komponen dimount
onMounted(fetchCategories)

// Fungsi untuk menambah/mengedit kategori
const saveCategory = () => {
  if (editingCategory.value) {
    // Update kategori
    const index = categories.value.findIndex(cat => cat.id === form.id)
    if (index !== -1) {
      categories.value[index].name = form.name
    }
  } else {
    // Tambah kategori baru
    const newCategory: Category = {
      id: Date.now(),
      name: form.name,
    }
    categories.value.push(newCategory)
  }

  // Simpan ke local storage dan reset form
  saveCategories()
  resetForm()
  showModal.value = false
}

// Fungsi untuk mengedit kategori
const editCategory = (category: Category) => {
  editingCategory.value = category
  form.id = category.id
  form.name = category.name
  showModal.value = true
}

// Fungsi untuk menghapus kategori
const deleteCategory = (id: number) => {
  categories.value = categories.value.filter(category => category.id !== id)
  saveCategories()
}

// Fungsi untuk mereset form
const resetForm = () => {
  editingCategory.value = null
  form.id = null
  form.name = ''
}
</script>

<template>
  <div class="container mx-auto px-4 py-8">
    <nav class="bg-gradient-to-r from-blue-500 to-indigo-600 p-6 rounded-lg shadow-lg flex justify-between items-center mb-8">
      <div>
        <h1 class="text-5xl text-white font-extrabold">myMoneii</h1>
        <p class="text-white text-lg font-light italic">Save More for A Better Future</p>
      </div>
      <div class="flex items-center space-x-4">
        <NuxtLink to="/" class="text-white hover:text-gray-300 text-lg transition duration-300">Dashboard</NuxtLink>
        <NuxtLink to="/transactions" class="text-white hover:text-gray-300 text-lg transition duration-300">Transaksi</NuxtLink>
        <NuxtLink to="/categories" class="text-white hover:text-gray-300 text-lg transition duration-300">Kategori</NuxtLink>
      </div>
    </nav>
    <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Daftar Kategori</h1>
    <div class="mb-4">
      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="showModal = true"
      >
        Tambah Kategori
      </button>
    </div>
    <div class="overflow-x-auto">
      <table class="w-full table-auto">
        <thead>
          <tr>
            <th class="px-4 py-2">Nama Kategori</th>
            <th class="px-4 py-2">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="category in categories" :key="category.id">
            <td class="border px-4 py-2">{{ category.name }}</td>
            <td class="border px-4 py-2">
              <button
                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded mr-2"
                @click="editCategory(category)"
              >
                Edit
              </button>
              <button
                class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
                @click="deleteCategory(category.id)"
              >
                Hapus
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Modal untuk menambah/mengedit kategori -->
    <div
      v-if="showModal"
      class="fixed z-10 inset-0 overflow-y-auto"
      aria-labelledby="modal-title"
      role="dialog"
      aria-modal="true"
    >
      <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
        <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" aria-hidden="true"></div>
        <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
        <div
          class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
        >
          <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
            <div class="sm:flex sm:items-start">
              <div class="mt-3 text-center sm:mt-0 sm:ml-4 sm:text-left">
                <h3 class="text-lg leading-6 font-medium text-gray-900" id="modal-title">
                  {{ editingCategory ? 'Edit Kategori' : 'Tambah Kategori' }}
                </h3>
                <div class="mt-2">
                  <div class="mb-4">
                    <label for="name" class="block text-gray-700 font-bold mb-2">Nama Kategori</label>
                    <input
                      v-model="form.name"
                      type="text"
                      id="name"
                      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    />
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
            <button
              type="button"
              class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-blue-600 text-base font-medium text-white hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 sm:ml-3 sm:w-auto sm:text-sm"
              @click="saveCategory"
            >
              {{ editingCategory ? 'Simpan Perubahan' : 'Tambah Kategori' }}
            </button>
            <button
              type="button"
              class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm"
              @click="showModal = false"
            >
              Batal
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
  </div>
</template>