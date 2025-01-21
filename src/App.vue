<script setup>
import { ref } from 'vue'
let array = ref(null)
const url = "https://www.amiiboapi.com/api/amiibo/"

const api = async () => {
  const response = await fetch(url)
    .then((resp) => resp.json())
    .then((resp) => resp.amiibo)
  array.value = response
}
api()
console.log(array)
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
    <div v-if="array != null">
      <div v-for="item in array" class="max-w-sm rounded overflow-hidden shadow-lg">
        <img class="w-full" :src="item.image">
        <div class="px-6 py-4">
          <div class="font-bold text-xl mb-2"></div>
          <p class="text-gray-700 text-base">
            {{ item.name }}
          </p>
        </div>
      </div>
    </div>
    <p v-else>Cargando...</p>
  </div>
</template>