<script setup>
import { ref } from 'vue'
let array = ref(null)
let personaje = ''
let url = "https://www.amiiboapi.com/api/amiibo/"

const api = async () => {
  const response = await fetch(url)
    .then((resp) => resp.json())
    .then((resp) => resp.amiibo)
  array.value = response
}
api()
function mostrarpersonaje(personaje){
  url = "https://www.amiiboapi.com/api/amiibo/?name=" + personaje
  api()
}
function limpiarfiltros(){
  url = "https://www.amiiboapi.com/api/amiibo/"
  personaje = ''
  api()
}
</script>

<template>

  <!--Object Atributes:
  "amiiboSeries":
  "character": 
  "gameSeries": 
  "head": 
  "image": 
  "name": 
  "release":  
  "tail": 
  "type": 
-->
  <div>
    <div v-if="array != null" >

      <header>
        <label for="filtrarnombre">Filtrar por nombre</label><br>
        <input type="text" name="filtrarnombre" id="filtrarnombre" placeholder="Mario" v-model="personaje" class=" p-2 border border-gray-300 rounded-lg mb-6" />
        <button @click="mostrarpersonaje(personaje)" type="button" name="buscar" id="buscar" class="mx-4 p-2 border border-gray-300 rounded-lg">Buscar</button>
        <button @click="limpiarfiltros" type="button" name="buscar" id="buscar" class="mx-4 p-2 border border-gray-300 rounded-lg">Limpiar filtros</button>
      </header>

      <div v-if="array != ''" class="place-self-center">
        <div class="container grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-12">
          <div v-for="item in array" class="rounded overflow-hidden shadow-lg place-self-center">
            <div class="flex justify-center">
              <img class="w-36 md:w-52 lg:w-64" :src="item.image">
            </div>
            <div class="px-6 py-4">
              <div class="text-xl mb-2 ">
                <p class="text-gray-700 text-base p-1">
                  Personaje:  <b>{{ item.character }}</b> 
                </p>
                <p class="text-gray-700 text-base p-1">
                  Nombre de personaje:  <b>{{ item.name }}</b> 
                </p>
                <p class="text-gray-700 text-base p-1">
                  Amiibo Series:  <b>{{ item.amiiboSeries }}</b> 
                </p>
                <p class="text-gray-700 text-base p-1">
                  Game Series:  <b>{{ item.gameSeries }}</b> 
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <p v-else>No se han encontrado amiibos similares a: {{ personaje }}</p>
    </div>
    <p v-else>Cargando...</p>
  </div>
</template>

<style scope>
</style>