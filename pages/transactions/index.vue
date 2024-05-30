<script setup lang="ts">
import { createClient } from '@supabase/supabase-js'
import { ref, reactive, onMounted } from 'vue'
import { parseISO, format } from 'date-fns'

interface Category {
  id: number;
  name: string;
}

interface Transaction {
  id: number;
  date: string;
  description: string;
  category: Category;
  amount: number;
}

// Supabase sementara, jangan lupa pindah ke file env
const supabase = createClient('https://vchyvlqxpntytsnpqzli.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InZjaHl2bHF4cG50eXRzbnBxemxpIiwicm9sZSI6ImFub24iLCJpYXQiOjE3MTY3ODEwMTUsImV4cCI6MjAzMjM1NzAxNX0.rXzpPTWkPH2sPbAukMsMXWt5lXGkvBR1WfAd2sC-3j8')

// State untuk transaksi
const transactions = ref<Transaction[]>([])
const showModal = ref(false)
const editingTransaction = ref<Transaction | null>(null)
const form = reactive({
  id: null as number | null,
  date: '',
  description: '',
  categoryId: null as number | null,
  amount: 0,
})

// State untuk kategori
const categories = ref<Category[]>([])

// Mengambil data transaksi dan kategori dari Supabase
onMounted(async () => {
  const { data: transactionsData } = await supabase.from('transactions').select('*, category(*)')
  if (transactionsData) transactions.value = transactionsData as Transaction[]

  const { data: categoriesData } = await supabase.from('categories').select('*')
  if (categoriesData) categories.value = categoriesData as Category[]
})

// Fungsi untuk format tanggal
const formatDate = (dateString: string) => {
  if (!dateString) return ''
  const date = parseISO(dateString)
  return format(date, 'dd/MM/yyyy')
}

// Fungsi untuk format mata uang
const formatCurrency = (amount: number) => {
  return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR' }).format(amount)
}

// Fungsi untuk menambah/mengedit transaksi
const saveTransaction = async () => {
  try {
    if (editingTransaction.value) {
      // Update transaksi
      await supabase
        .from('transactions')
        .update({
          date: form.date,
          description: form.description,
          category_id: form.categoryId,
          amount: form.amount,
        })
        .eq('id', form.id)
    } else {
      // Tambah transaksi baru
      const { data, error } = await supabase
        .from('transactions')
        .insert([
          {
            date: form.date,
            description: form.description,
            category_id: form.categoryId,
            amount: form.amount,
          },
        ])

      if (error) console.error(error)
    }

    // Reset form dan tutup modal
    resetForm()
    showModal.value = false

    // Mengambil data transaksi terbaru
    const { data: transactionsData } = await supabase.from('transactions').select('*, category(*)')
    if (transactionsData) transactions.value = transactionsData as Transaction[]
  } catch (error) {
    console.error(error)
  }
}

// Fungsi untuk mengedit transaksi
const editTransaction = (transaction: Transaction) => {
  editingTransaction.value = transaction
  form.id = transaction.id
  form.date = transaction.date
  form.description = transaction.description
  form.categoryId = transaction.category.id
  form.amount = transaction.amount
  showModal.value = true
}

// Fungsi untuk menghapus transaksi
const deleteTransaction = async (id: number) => {
  try {
    await supabase.from('transactions').delete().eq('id', id)

    // Mengambil data transaksi terbaru
    const { data: transactionsData } = await supabase.from('transactions').select('*, category(*)')
    if (transactionsData) transactions.value = transactionsData as Transaction[]
  } catch (error) {
    console.error(error)
  }
}

// Fungsi untuk mereset form
const resetForm = () => {
  editingTransaction.value = null
  form.id = null
  form.date = ''
  form.description = ''
  form.categoryId = null
  form.amount = 0
}
</script>


<template>
  <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Daftar Transaksi</h1>
    <div class="mb-4">
      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="showModal = true"
      >
        Tambah Transaksi
      </button>
    </div>
    <div class="overflow-x-auto">
      <table class="w-full table-auto">
        <thead>
          <tr>
            <th class="px-4 py-2">Tanggal</th>
            <th class="px-4 py-2">Deskripsi</th>
            <th class="px-4 py-2">Kategori</th>
            <th class="px-4 py-2">Jumlah</th>
            <th class="px-4 py-2">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="transaction in transactions" :key="transaction.id">
            <td class="border px-4 py-2">{{ formatDate(transaction.date) }}</td>
            <td class="border px-4 py-2">{{ transaction.description }}</td>
            <td class="border px-4 py-2">{{ transaction.category.name }}</td>
            <td
              class="border px-4 py-2"
              :class="{
                'text-green-500': transaction.amount > 0,
                'text-red-500': transaction.amount < 0,
              }"
            >
              {{ formatCurrency(transaction.amount) }}
            </td>
            <td class="border px-4 py-2">
              <button
                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded mr-2"
                @click="editTransaction(transaction)"
              >
                Edit
              </button>
              <button
                class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
                @click="deleteTransaction(transaction.id)"
              >
                Hapus
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Modal untuk menambah/mengedit transaksi -->
    <div
      v-if="showModal"
      class="fixed z-10 inset-0 overflow-y-auto"
      aria-labelledby="modal-title"
      role="dialog"
      aria-modal="true"
    >
      <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
        <div
          class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
          aria-hidden="true"
        ></div>
        <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
        <div
          class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
        >
          <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
            <div class="sm:flex sm:items-start">
              <div class="mt-3 text-center sm:mt-0 sm:ml-4 sm:text-left">
                <h3 class="text-lg leading-6 font-medium text-gray-900" id="modal-title">
                  {{ editingTransaction ? 'Edit Transaksi' : 'Tambah Transaksi' }}
                </h3>
                <div class="mt-2">
                  <div class="mb-4">
                    <label for="date" class="block text-gray-700 font-bold mb-2">Tanggal</label>
                    <input
                      v-model="form.date"
                      type="date"
                      id="date"
                      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    />
                  </div>
                  <div class="mb-4">
                    <label for="description" class="block text-gray-700 font-bold mb-2">Deskripsi</label>
                    <input
                      v-model="form.description"
                      type="text"
                      id="description"
                      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    />
                  </div>
                  <div class="mb-4">
                    <label for="category" class="block text-gray-700 font-bold mb-2">Kategori</label>
                    <select
                      v-model="form.categoryId"
                      id="category"
                      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    >
                      <option v-for="category in categories" :key="category.id" :value="category.id">
                        {{ category.name }}
                      </option>
                    </select>
                  </div>
                  <div class="mb-4">
                    <label for="amount" class="block text-gray-700 font-bold mb-2">Jumlah</label>
                    <input
                      v-model.number="form.amount"
                      type="number"
                      id="amount"
                      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    />
                  </div>
                </div>
              </div>
            </div>
            <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
              <button
                type="button"
                class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-blue-600 text-base font-medium text-white hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 sm:ml-3 sm:w-auto sm:text-sm"
                @click="saveTransaction"
              >
                {{ editingTransaction ? 'Simpan Perubahan' : 'Tambah Transaksi' }}
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

<style></style>