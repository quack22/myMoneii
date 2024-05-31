<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { Chart, registerables } from 'chart.js';
Chart.register(...registerables);

const incomeData = ref([300, 400, 200, 500, 700, 600, 800]);
const expenseData = ref([200, 150, 300, 250, 100, 300, 200]);
const balanceData = ref([100, 250, 100, 250, 600, 300, 600]);

onMounted(() => {
  const incomeCanvas = document.getElementById('incomeChart');
  if (incomeCanvas instanceof HTMLCanvasElement) {
    const ctx1 = incomeCanvas.getContext('2d');
    if (ctx1) {
      new Chart(ctx1, {
        type: 'line',
        data: {
          labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul'],
          datasets: [{
            label: 'Pemasukan',
            data: incomeData.value,
            borderColor: 'rgba(75, 192, 192, 1)',
            backgroundColor: 'rgba(75, 192, 192, 0.2)',
            fill: true,
          }],
        },
        options: {
          responsive: true,
          plugins: {
            legend: {
              position: 'top',
            },
            tooltip: {
              mode: 'index',
              intersect: false,
            },
          },
          hover: {
            mode: 'nearest',
            intersect: true,
          },
          scales: {
            x: {
              display: true,
              title: {
                display: true,
                text: 'Bulan',
              },
            },
            y: {
              display: true,
              title: {
                display: true,
                text: 'Jumlah',
              },
            },
          },
        },
      });
    }
  }

  const expenseCanvas = document.getElementById('expenseChart');
  if (expenseCanvas instanceof HTMLCanvasElement) {
    const ctx2 = expenseCanvas.getContext('2d');
    if (ctx2) {
      new Chart(ctx2, {
        type: 'bar',
        data: {
          labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul'],
          datasets: [{
            label: 'Pengeluaran',
            data: expenseData.value,
            backgroundColor: 'rgba(255, 99, 132, 0.2)',
            borderColor: 'rgba(255, 99, 132, 1)',
            borderWidth: 1,
          }],
        },
        options: {
          responsive: true,
          plugins: {
            legend: {
              position: 'top',
            },
            tooltip: {
              mode: 'index',
              intersect: false,
            },
          },
          scales: {
            x: {
              display: true,
              title: {
                display: true,
                text: 'Bulan',
              },
            },
            y: {
              display: true,
              title: {
                display: true,
                text: 'Jumlah',
              },
            },
          },
        },
      });
    }
  }

  const balanceCanvas = document.getElementById('balanceChart');
  if (balanceCanvas instanceof HTMLCanvasElement) {
    const ctx3 = balanceCanvas.getContext('2d');
    if (ctx3) {
      new Chart(ctx3, {
        type: 'pie',
        data: {
          labels: ['Saldo Januari', 'Saldo Februari', 'Saldo Maret', 'Saldo April', 'Saldo Mei', 'Saldo Juni', 'Saldo Juli'],
          datasets: [{
            label: 'Saldo',
            data: balanceData.value,
            backgroundColor: [
              'rgba(54, 162, 235, 0.2)',
              'rgba(255, 206, 86, 0.2)',
              'rgba(75, 192, 192, 0.2)',
              'rgba(153, 102, 255, 0.2)',
              'rgba(255, 159, 64, 0.2)',
              'rgba(201, 203, 207, 0.2)',
              'rgba(255, 99, 132, 0.2)',
            ],
            borderColor: [
              'rgba(54, 162, 235, 1)',
              'rgba(255, 206, 86, 1)',
              'rgba(75, 192, 192, 1)',
              'rgba(153, 102, 255, 1)',
              'rgba(255, 159, 64, 1)',
              'rgba(201, 203, 207, 1)',
              'rgba(255, 99, 132, 1)',
            ],
            borderWidth: 1,
          }],
        },
        options: {
          responsive: true,
          plugins: {
            legend: {
              position: 'top',
            },
            tooltip: {
              mode: 'index',
              intersect: false,
            },
          },
        },
      });
    }
  }
});
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
    <div class="text-center mb-12">
      <h1 class="text-4xl font-extrabold mb-4">Ringkasan Keuangan</h1>
      <p class="text-gray-600 text-lg">Ikhtisar cepat tentang keuangan Anda saat ini</p>
    </div>
    <div class="grid grid-cols-1 md:grid-cols-3 gap-8 mb-12">
      <div class="bg-green-500 text-white p-6 rounded-lg shadow-lg">
        <h2 class="text-xl font-bold mb-2">Total Pemasukan</h2>
        <p class="text-3xl font-extrabold">$30</p>
      </div>
      <div class="bg-red-500 text-white p-6 rounded-lg shadow-lg">
        <h2 class="text-xl font-bold mb-2">Total Pengeluaran</h2>
        <p class="text-3xl font-extrabold">$2</p>
      </div>
      <div class="bg-blue-500 text-white p-6 rounded-lg shadow-lg">
        <h2 class="text-xl font-bold mb-2">Saldo Saat Ini</h2>
        <p class="text-3xl font-extrabold">$100</p>
      </div>
    </div>
    <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
      <div class="bg-white p-6 rounded-lg shadow-lg">
        <h2 class="text-xl font-bold mb-4">Grafik Pemasukan</h2>
        <canvas id="incomeChart"></canvas>
      </div>
      <div class="bg-white p-6 rounded-lg shadow-lg">
        <h2 class="text-xl font-bold mb-4">Grafik Pengeluaran</h2>
        <canvas id="expenseChart"></canvas>
      </div>
      <div class="bg-white p-6 rounded-lg shadow-lg col-span-full">
        <h2 class="text-xl font-bold mb-4">Grafik Saldo</h2>
        <canvas id="balanceChart"></canvas>
      </div>
    </div>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;800&display=swap');

body {
  font-family: 'Poppins', sans-serif;
  background-color: #f4f4f9;
  margin: 0;
  padding: 0;
}
</style>