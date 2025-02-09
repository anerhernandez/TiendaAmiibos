<script setup>
import { ref, computed } from 'vue'
import { jsx } from 'vue/jsx-runtime'

let array = ref(null) // Aquí se almacenan todos los amiibos
let currentPage = ref(1) // Página actual
let itemsPerPage = 15 // Número de amiibos por página
let personaje = '' // Filtro por nombre
let url = "https://www.amiiboapi.com/api/amiibo/"
let carrito = ref(null)

// Función para obtener los datos de la API
const api = async () => {
  const response = await fetch(url)
    .then((resp) => resp.json())
    .then((resp) => resp.amiibo)
  array.value = response
}

// Llama a la API para cargar los datos iniciales
api()

// Filtrar por personaje
function mostrarpersonaje(personaje) {
  url = "https://www.amiiboapi.com/api/amiibo/?name=" + personaje
  currentPage.value = 1 // Reinicia la página al aplicar un filtro
  api()
}
//Function that loads and shows shopping cart
function loadshoppingcart(){
  document.getElementById("amiibos").innerHTML=""
  JSON.parse(localStorage.getItem("amiibos")).forEach(element => {
      let texto = document.createElement("p")
      texto.textContent = element;
      document.getElementById("amiibos").appendChild(texto)
    });
}

// Limpiar filtros
function limpiarfiltros() {
  url = "https://www.amiiboapi.com/api/amiibo/"
  personaje = ''
  currentPage.value = 1 // Reinicia la página al limpiar filtros
  api()
}
loadshoppingcart()

function mostrarcarrito(){
  document.getElementById("carrito").setAttribute("style", "display:inline")
  document.getElementById("bcarritO").setAttribute("style", "display:inline")
  document.getElementById("bcarritoM").setAttribute("style", "display:none")
}
function ocultarcarrito(){
  document.getElementById("carrito").setAttribute("style", "display:none")
  document.getElementById("bcarritO").setAttribute("style", "display:none")
  document.getElementById("bcarritoM").setAttribute("style", "display:inline")
}
function addtocart(amiibo){
   alert("Se ha añadido al carrito: " + amiibo)
   if (localStorage.getItem("amiibos") === null) {
     localStorage.setItem("amiibos", JSON.stringify([amiibo]));
     carrito = JSON.parse(localStorage.getItem("amiibos"));
   }else{
     carrito = JSON.parse(localStorage.getItem("amiibos"));
     carrito.push(amiibo)
     localStorage.setItem("amiibos", JSON.stringify(carrito))
   }
   loadshoppingcart()
}

// Propiedad computada para obtener los amiibos de la página actual
const paginatedArray = computed(() => {
  if (!array.value) return []
  const start = (currentPage.value - 1) * itemsPerPage
  const end = start + itemsPerPage
  return array.value.slice(start, end)
})

// Calcula el total de páginas basado en el array
const totalPages = computed(() => {
  return array.value ? Math.ceil(array.value.length / itemsPerPage) : 0
})
</script>

<template>
  <div class="dark:bg-neutral-800 dark:text-white">
    <div v-if="array != null">
      <!-- Filtros -->
      <header>
        <label class="text-lg" for="filtrarnombre">Filtrar por nombre</label><br>
        <input type="text" name="filtrarnombre" id="filtrarnombre" placeholder="Mario" v-model="personaje"
          class="p-2 m-4 border border-gray-300 rounded-lg" />
        <button @click="mostrarpersonaje(personaje)" type="button" name="buscar" id="buscar"
          class="mx-4 p-2 border border-gray-300 rounded-lg">
          Buscar
        </button>
        <button @click="limpiarfiltros" type="button" name="limpiar" id="limpiar"
          class="mx-4 p-2 border border-gray-300 rounded-lg">
          Limpiar filtros
        </button>
        <button @click="mostrarcarrito" type="button" name="bcarritoM" id="bcarritoM"
          class="mx-4 p-2 border border-gray-300 rounded-lg">
          Mostrar carrito
        </button>
        <button @click="ocultarcarrito" type="button" name="bcarritO" id="bcarritO"
          class="mx-4 p-2 border border-gray-300 rounded-lg hidden">
          Ocultar carrito
        </button>
      </header>

      <!-- No se ejecuta la muestra ni páginas si el amiibo buscado no existe -->
      <div v-if="array != ''">
        <!-- Listado de Amiibos -->
        <div v-if="paginatedArray.length > 0" class="place-self-center dark:bg-neutral-800">
          <div class="container grid grid-cols-1 xl:grid-cols-2 2xl:grid-cols-3 gap-12">
            <div v-for="(item, index) in paginatedArray" :key="index"
              class="rounded overflow-hidden shadow-lg place-self-center dark:bg-neutral-900 w-96">
              <div class="flex justify-center">
                <img class="w-36 md:w-52 lg:w-64" :src="item.image" />
              </div>
              <div class="px-6 py-4">
                <div class="text-xl mb-2 ">
                  <p class="text-gray-700 text-base p-1 dark:text-white">
                    Personaje: <b>{{ item.character }}</b>
                  </p>
                  <p class="text-gray-700 text-base p-1 dark:text-white">
                    Nombre de personaje: <b>{{ item.name }}</b>
                  </p>
                  <p class="text-gray-700 text-base p-1 dark:text-white">
                    Amiibo Series: <b>{{ item.amiiboSeries }}</b>
                  </p>
                  <p class="text-gray-700 text-base p-1 dark:text-white">
                    Game Series: <b>{{ item.gameSeries }}</b>
                  </p>
                  <button :value=item.name @click="addtocart(item.name)" class="dark:bg-neutral-800">Añadir a carrito</button>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Controles de Paginación -->
        <div class="flex justify-center mt-6 fixed bottom-0 left-0 right-0 bg-neutral-100">
          <button @click="currentPage--" :disabled="currentPage === 1"
            class="px-4 py-2 m-1 bg-gray-300 rounded hover:bg-gray-400 disabled:opacity-50">
            Anterior
          </button>
          <span class="px-4 py-2 m-1">Página {{ currentPage }} de {{ totalPages }}</span>
          <button @click="currentPage++" :disabled="currentPage === totalPages"
            class="px-4 py-2 m-1 bg-gray-300 rounded hover:bg-gray-400 disabled:opacity-50">
            Siguiente
          </button>
        </div>
      </div>
      <p v-else>No se encontraron amiibos con el nombre <b>{{ personaje }}</b></p>
    </div>
    <p v-else>Cargando...</p>
  </div>
</template>
<style scoped></style>
