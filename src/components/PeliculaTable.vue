<template>
    <div class="q-pa-md">
      <q-table
        flat bordered
        title="PELÍCULAS"
        :rows="filteredRows"
        :columns="columns"
        row-key="id"
        :selected-rows-label="getSelectedString"
        selection="multiple"
        v-model:selected="selected"
      />
      <!-- :rows="filteredRows" -->
      <!-- :rows="rows" -->
  
      <!-- <div class="q-mt-md">
        Selected: {{ JSON.stringify(selected) }}
      </div> -->
    </div>
</template>

<style>

</style>

<script>
import { ref, onMounted, computed } from 'vue'
import axios from 'axios'

export default {
  props: {searchQuery: String},
  setup(props) {
    const selected = ref([])
    const rows = ref([])

    const columns = [
      { name: 'title', align: 'left', label: 'Título', field: 'title', sortable: true },
      { name: 'release_date', label: 'Fecha Lanzamiento', field: 'release_date', sortable: true },
      { name: 'popularity', label: 'Popularidad', field: 'popularity', sortable: true }
    ]

    const fetchMovies = async () => {
      try {
        const response = await axios.get('https://api.themoviedb.org/3/discover/movie', {
          headers: {
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI2ZGExYjA2ODUwOGE3ZGEwNmU1Mzc0MWJiYTQ1NWVmOCIsInN1YiI6IjY1NWFmYzI1MmIyMTA4MDBhZGIxMmYyNiIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.XOHwmgtIRxUIpmaEbxHzYPcrqinZyMLNNYUbmM3-BTo',
          },
          params: {
            api_key: '6da1b068508a7da06e53741bba455ef8',
          }
        })
        rows.value = response.data.results
      } catch (error) {
        console.error('Error al obtener películas:', error)
      }
    }

    const filteredRows = computed(() => {
      if (!props.searchQuery) {
        return rows.value;
      }
      return rows.value.filter(row => row.title.toLowerCase().includes(props.searchQuery.toLowerCase()));
    });

    onMounted(() => {
      fetchMovies()
    })

    return {
      selected,
      columns,
      rows,
      filteredRows
    }
  }
}
</script>


