<template>
    <div class="p-4 space-y-4">
      <!-- Search Bar -->
      <input
        v-model="search"
        placeholder="Search..."
        class="border border-gray-300 rounded-lg p-2 flex-1"
      />

      <!-- Kecamatan Filter Dropdown -->
      <select
        v-model="selectedKecamatan"
        class="border border-gray-300 rounded-lg p-2"
      >
        <option value="">All Kecamatan</option>
        <option v-for="kecamatan in uniqueKecamatan" :key="kecamatan" :value="kecamatan">
          {{ kecamatan }}
        </option>
      </select>

      <!-- Data Table -->
      <div class="overflow-x-auto">
        <table class="min-w-full bg-white border border-gray-200 rounded-lg">
          <thead>
            <tr class="bg-gray-100 text-gray-700">
              <th class="p-3 text-left">Kecamatan</th>
              <th class="p-3 text-left">Kelurahan</th>
              <th class="p-3 text-left">NoTps</th>
              <th class="p-3 text-left">Alamat</th>
              <th class="p-3 text-left">Location</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(item, index) in filteredData"
              :key="index"
              class="border-t hover:bg-gray-50"
            >
              <td class="p-3">{{ item.kecamatan }}</td>
              <td class="p-3">{{ item.kelurahan }}</td>
              <td class="p-3">{{ item.noTps }}</td>
              <td class="p-3">{{ item.alamat }}</td>
              <td class="p-3">
                <a
                  :href="`https://www.google.com/maps?q=${item.lat},${item.lon}`"
                  target="_blank"
                  class="text-blue-500 hover:underline"
                >
                  View on Map
                </a>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </template>

  <script setup lang="ts">
  import { ref, computed, onMounted } from 'vue';

  // Data Interface for TypeScript
  interface DataTps {
    kecamatan: string;
    kelurahan: string;
    noTps: string;
    alamat: string;
    lat: string;
    lon: string;
  }

  // State variables
  const data = ref<DataTps[]>([]);             // Stores the fetched data
  const search = ref<string>('');              // Search input
  const selectedKecamatan = ref<string>('');   // Filter for kecamatan

  // Computed property for unique kecamatan values
  const uniqueKecamatan = computed(() => {
    return [...new Set(data.value.map(item => item.kecamatan))];
  });

  // Computed property for filtered data
  const filteredData = computed(() => {
    return data.value.filter((item) => {
      const matchesSearch = Object.values(item).some((value) =>
        value.toString().toLowerCase().includes(search.value.toLowerCase())
      );
      const matchesKecamatan = selectedKecamatan.value
        ? item.kecamatan === selectedKecamatan.value
        : true;
      return matchesSearch && matchesKecamatan;
    });
  });

  // Fetch the JSON data
  const loadData = async () => {
    const response = await fetch('/data.json');
    const jsonData = await response.json();
    data.value = jsonData.dataTps.map((item: DataTps) => ({
      ...item,
      lat: parseFloat(item.lat),
      lon: parseFloat(item.lon),
    }));
  };

  // Load data when component is mounted
  onMounted(() => {
    loadData();
  });
  </script>

  <style scoped>
  /* Additional custom styling if needed */
  </style>
