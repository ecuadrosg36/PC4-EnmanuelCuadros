<template>
    <q-page padding>
        <h4>LISTADO DE PELÍCULAS FAVORITAS</h4>

        <pelicula-favorita-table/>

    </q-page>
</template>

<style>

</style>

<script>
import { ssrProductionExport } from 'quasar/wrappers'
import { ref, onMounted } from 'vue'
import { useQuasar } from 'quasar'
import PeliculaFavoritaTable from 'src/components/PeliculaFavoritaTable.vue'
import axios from 'axios'

export default {
    components: { PeliculaFavoritaTable }
}

</script>