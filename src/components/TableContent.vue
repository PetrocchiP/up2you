<template>
  <div v-if="isLoading" class="min-h-screen flex flex-col items-center justify-center">
    <div class="loader"></div>
  </div>
  <div class="container mx-auto mt-10" v-if="!isLoading">
    <div class="mb-4 flex justify-end relative">
      <input
        type="text"
        v-model="searchTerm"
        placeholder="Cerca"
        class="px-4 py-2 bg-gray-200 rounded"
      />
    </div>
    <div class="overflow-x-auto">
      <table class="min-w-full mt-4 table-auto rounded-lg">
        <thead>
          <tr class="table__head--text text-left">
            <th></th>
            <th>ID</th>
            <th>Azienda</th>
            <th>CO₂ (kg)</th>
            <th>Progetto</th>
            <th>Tipo</th>
            <th>Origine</th>
            <th>Data</th>
          </tr>
        </thead>
        <tbody class="bg-white">
          <tr v-for="item in paginatedData" :key="item.internal_id" class="border-b border-table items-center">
            <td>
              <img :src="item.image" width="40" class="rounded ms-2">
            </td>
            <td>{{ item.internal_id }}</td>
            <td>
              <div class="flex items-center">
                <span><img :src="item.details.entity_logo" width="20" class="mr-2"></span>
                <span>{{ item.details.entity_name }}</span>
              </div>
            </td>
            <td>{{ item.formatted_quantity_kg }}</td>
            <td>
              <p>{{ item.details.project_name }}</p>
              <small class="text-gray-400">Verified Carbon Standard</small>
            </td>
            <td>{{ item.details.project_type }}</td>
            <td>
              <span :class="`flag-icon flag flag-icon-${item.details.country_code.toLowerCase()}`"></span>
              {{ item.details.country_name }}
            </td>
            <td>{{ formatDate(item.details.transaction_datetime) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="mt-4 flex justify-end items-center">
      <button
        @click="previousPage"
        :disabled="currentPage === 1"
        class="px-4 py-2 border border-table rounded">
        Indietro
      </button>
      <span class="mx-4">Pagina {{ currentPage }} di {{ totalPages }}</span>
      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="px-4 py-2 border border-table rounded">
        Avanti
      </button>
    </div>
  </div>
</template>

<script>
import dayjs from 'dayjs';
import 'dayjs/locale/it';

export default {
  data() {
    return {
      items: [],
      searchTerm: "",
      pageSize: 8,
      currentPage: 1,
      isLoading: false,
    };
  },
  computed: {
    filteredData() {
      const term = this.searchTerm.toLowerCase();
      return this.items.filter(
        (item) =>
          item.details.entity_name.toLowerCase().includes(term)  ||
          item.details.project_name.toLowerCase().includes(term) ||
          item.internal_id.toString().includes(term) ||
          item.details.country_name.toLowerCase().includes(term) ||
          item.formatted_quantity_kg.toLowerCase().includes(term)
      );
    },
    totalPages() {
      return Math.ceil(this.filteredData.length / this.pageSize);
    },
    paginatedData() {
      const startIndex = (this.currentPage - 1) * this.pageSize;
      const endIndex = startIndex + this.pageSize;
      return this.filteredData.slice(startIndex, endIndex);
    },
  },
  methods: {
    fetchData() {
      this.isLoading = true;
      fetch('https://technical-interview-production.up.railway.app/nft')
        .then(response => response.json())
        .then(data => {
          this.items = data.data;
          this.isLoading = false;
        })
        .catch(error => {
          this.isLoading = false;
          console.error(error);
        });
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    formatDate(date) {
      dayjs.locale('it');
      return dayjs(date).format('DD MMM YYYY');
    },
  },
  mounted() {
    this.fetchData();
  },
};
</script>