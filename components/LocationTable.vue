<template>
  <div class="pa-md-4">

    <!-- Kecamatan Filter Dropdown -->
    <v-select
      v-model="selectedKecamatan"
      :items="uniqueKecamatan"
      label="Filter by Kecamatan"
      clearable
      outlined
    />

    <!-- Search Input -->
    <v-text-field
      v-model="search"
      label="Search"
      outlined
    />

    <!-- Data Table -->
    <v-data-table
      density="comfortable"
      :headers="headers"
      :items="filteredData"
      item-value="noTps"
      class="elevation-1"
    >
      <template #item.location="{ item }">
        <a
          :href="`https://www.google.com/maps?q=${item.lat},${item.lon}`"
          target="_blank"
          class="text-blue-500"
        >
          View on Map
        </a>
      </template>
    </v-data-table>
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
const selectedKecamatan = ref<string | null>(null);

// Define table headers
const headers = [
  { title: 'Kecamatan', value: 'kecamatan' },
  { title: 'Kelurahan', value: 'kelurahan' },
  { title: 'No TPS', value: 'noTps' },
  { title: 'Alamat', value: 'alamat' },
  { title: 'Lokasi', value: 'location' }
];

// Computed property for unique Kecamatan options
const uniqueKecamatan = computed(() =>
  [...new Set(data.value.map(item => item.kecamatan))]
);

// Computed property to filter data based on search and kecamatan
const filteredData = computed(() =>
  data.value.filter(item => {
    const matchesSearch = Object.values(item).some(value =>
      value.toString().toLowerCase().includes(search.value.toLowerCase())
    );
    const matchesKecamatan = selectedKecamatan.value
      ? item.kecamatan === selectedKecamatan.value
      : true;
    return matchesSearch && matchesKecamatan;
  })
);

// Fetch data from JSON
const loadData = async () => {
  const response = await fetch('/data.json');
  const jsonData = await response.json();
  data.value = jsonData.dataTps.map((item: DataTps) => ({
    ...item,
    noTps: item.noTps.replace(/^\d+-/, ''), // Removes the prefix before the hyphen
    kecamatan: item.kecamatan.replace(/^\d+-/, ''), // Removes the prefix before the hyphen
    kelurahan: item.kelurahan.replace(/^\d+-/, ''), // Removes the prefix before the hyphen
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
/* Optional styles */
</style>
