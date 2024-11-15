<template>
  <div class="p-4 space-y-4">
    <!-- Search Input -->
    <el-input
      v-model="search"
      placeholder="Search..."
      class="w-full"
      clearable
    />

    <!-- Kecamatan Filter Dropdown -->
    <el-select
      v-model="selectedKecamatan"
      placeholder="Select Kecamatan"
      class="w-full"
      clearable
    >
      <el-option
        v-for="kecamatan in uniqueKecamatan"
        :key="kecamatan"
        :label="kecamatan"
        :value="kecamatan"
      />
    </el-select>

    <!-- Data Table -->
    <el-table :data="filteredData" style="width: 100%">
      <el-table-column prop="kecamatan" label="Kecamatan" />
      <el-table-column prop="kelurahan" label="Kelurahan" />
      <el-table-column prop="noTps" label="NoTps" />
      <el-table-column prop="alamat" label="Alamat" />
      <el-table-column label="Location">
        <template #default="{ row }">
          <a
            :href="`https://www.google.com/maps?q=${row.lat},${row.lon}`"
            target="_blank"
            class="text-blue-500 hover:underline"
          >
            View on Map
          </a>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';

// Define TypeScript interface for data structure
interface DataTps {
  kecamatan: string;
  kelurahan: string;
  noTps: string;
  alamat: string;
  lat: string;
  lon: string;
}

// Reactive variables
const data = ref<DataTps[]>([]);
const search = ref<string>('');
const selectedKecamatan = ref<string>('');

// Computed property for unique Kecamatan options
const uniqueKecamatan = computed(() => {
  return [...new Set(data.value.map(item => item.kecamatan))];
});

// Computed property to filter data based on search and kecamatan
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

// Function to load data from JSON file
const loadData = async () => {
  const response = await fetch('/data.json');
  const jsonData = await response.json();
  data.value = jsonData.dataTps.map((item: DataTps) => ({
    ...item,
    lat: parseFloat(item.lat),
    lon: parseFloat(item.lon),
  }));
};

// Fetch data on component mount
onMounted(() => {
  loadData();
});
</script>

<style scoped>
/* Optional additional styles */
</style>
