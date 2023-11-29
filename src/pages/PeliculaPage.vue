<template>
    <q-page padding>
        <h4>LISTADO DE PELÍCULAS</h4>

        <!-- <pre>{{pelicula}} - {{terminos}}</pre> -->

        <q-form 
            class="row q-col-gutter-md" 
            @submit.prevent="procesarFormulario"
            @reset="reset"
            ref="myForm"
        >

            <div class="col-12 col-sm-6">
                <q-input 
                    v-model="pelicula" 
                    label="Pelicula"
                    lazy-rules
                />
                <!-- :rules="[ val => val && val.length > 0 || 'Por favor completar el campo de Pelicula']" -->
                <!-- @update:modelValue="filtrarPeliculas" -->

            </div>

            <div class="col-12">
                <q-btn 
                    label="AGREGAR"    
                    color="teal-10"
                    type="submit"
                />
                <q-btn 
                    label="RESETEAR"    
                    color="red-10" 
                    outline
                    class="q-ml-sm"
                    type="reset"
                />
            </div>

            <div class="col-12">
                <q-toggle
                    label="Aceptar los términos"
                    v-model="terminos"
                />
            </div>
        </q-form> 

        <pelicula-table :search-query="pelicula"/>
        <!-- <pelicula-table/> -->
        <!-- <pelicula-table :filtered-rows="filteredRows"/> -->

    </q-page>
</template>

<style>

</style>

<script>
import { ssrProductionExport } from 'quasar/wrappers'
import { ref, onMounted } from 'vue'
import { useQuasar } from 'quasar'
import PeliculaTable from 'src/components/PeliculaTable.vue'
import axios from 'axios'

export default {
    components: { PeliculaTable },
    setup() {
        const $q = useQuasar()
        const myForm = ref(null)
        const pelicula = ref('')
        const terminos = ref(false)

        // Función para obtener el ID de la película
        async function obtenerIdDePelicula(nombrePelicula) {
            try {
                console.log(`Buscando película: ${nombrePelicula}`);
                const response = await axios.get(`https://api.themoviedb.org/3/search/movie?api_key=6da1b068508a7da06e53741bba455ef8&query=${encodeURIComponent(nombrePelicula)}`);
                console.log(response.data);
                if (response.data.results.length > 0) {
                    return response.data.results[0].id;
                } else {
                    throw new Error('Película no encontrada');
                }
            } catch (error) {
                console.error('Error al buscar la película:', error);
                throw error;
            }
        }

        // Modificación del método procesarFormulario
        const procesarFormulario = async () => {
            if(terminos.value) {
                try {
                    const movieId = await obtenerIdDePelicula(pelicula.value);
                    await axios.post(`https://api.themoviedb.org/3/account/20723838/favorite`, {
                        media_type: 'movie',
                        media_id: movieId,
                        favorite: true
                    }, {
                        headers: {
                            'Content-Type': 'application/json',
                            'Authorization': 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI2ZGExYjA2ODUwOGE3ZGEwNmU1Mzc0MWJiYTQ1NWVmOCIsInN1YiI6IjY1NWFmYzI1MmIyMTA4MDBhZGIxMmYyNiIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.XOHwmgtIRxUIpmaEbxHzYPcrqinZyMLNNYUbmM3-BTo'
                        }
                    });

                    $q.notify({
                        color: 'green-4',
                        textColor: 'white',
                        icon: 'cloud_done',
                        message: 'Agregado a favoritos'
                    });
                } catch (error) {
                    console.error('Error al agregar a favoritos:', error);
                    $q.notify({
                        color: 'red-5',
                        textColor: 'white',
                        icon: 'warning',
                        message: 'Error al agregar a favoritos'
                    });
                }
            } else {
                $q.notify({
                    color: 'red-5',
                    textColor: 'white',
                    icon: 'warning',
                    message: 'Deberá aceptar los términos'
                });
            }
            myForm.value.resetValidation();
            reset();
        }

        // const procesarFormulario = () => {
        //     console.log('Diste click al formulario')
        //     if(terminos.value === false){
        //         $q.notify({
        //             color: 'red-5',
        //             textColor: 'white',
        //             icon: 'warning',
        //             message: 'Deberá aceptar los términos'
        //         })
        //     }else {
        //         $q.notify({
        //             color: 'green-4',
        //             textColor: 'white',
        //             icon: 'cloud_done',
        //             message: 'Agregado a favoritos'
        //         })
        //     }
        //     myForm.value.resetValidation()
        //     reset()
        // }

        const reset = () => {
            pelicula.value = null
            terminos.value = false
        }

        return {
            myForm,
            pelicula,
            terminos,
            procesarFormulario,
            reset
        }
    }
}
</script>