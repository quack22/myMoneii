<script setup lang="ts">

</script>

<template>
  <div class="container mx-auto">
    <nav class="bg-gray-800 p-4 flex justify-between">
      <div>
        <h1 class="text-4xl text-white font-bold">myMoneii</h1>
        <p class="text-white font-serif">Save More for A Better Future</p>
      </div>
      <div class="flex items-center">
        <NuxtLink to="/" class="text-white hover:text-gray-300 mx-2">Dashboard</NuxtLink>
        <NuxtLink to="/transactions" class="text-white hover:text-gray-300 mx-2">Transaksi</NuxtLink>
        <NuxtLink to="/categories" class="text-white hover:text-gray-300 mx-2">Kategori</NuxtLink>
      </div>
    </nav>
    <h1 class="text-2xl font-bold mb-4 mt-4">Ringkasan Keuangan</h1>
    <div class="grid grid-cols-3 gap-4">
      <div class="bg-green-100 p-4 rounded-md">
        <h2 class="text-lg font-bold">Total Pemasukan</h2>
        <p class="text-2xl font-bold">$30</p>
      </div>
      <div class="bg-red-100 p-4 rounded-md">
        <h2 class="text-lg font-bold">Total Pengeluaran</h2>
        <p class="text-2xl font-bold">$2</p>
      </div>
      <div class="bg-blue-100 p-4 rounded-md">
        <h2 class="text-lg font-bold">Saldo Saat Ini</h2>
        <p class="text-2xl font-bold">$100</p>
      </div>
    </div>
  </div>

</template>

<style>
</style>