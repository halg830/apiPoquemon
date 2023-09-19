<script setup>
import detalle from './components/detalle.vue'
import axios from 'axios';
import {ref} from 'vue'
const pokemones = ref([])

async function obtenerUrlsPokemon(i) {
  const pokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}/`)
  console.log(pokemon)
  pokemones.value.push ({
    id: pokemon.data.id,
    imagen: pokemon.data.sprites.other['official-artwork'].front_shiny,
    name: pokemon.data.name,
    altura: pokemon.data.height,
    peso: pokemon.data.weight,
    estadisticas: pokemon.data.stats.map((e) => {
      return { name: e.stat.name, cant: e.base_stat }
    }),
    tipos: pokemon.data.types.map(e => e.type.name)
  })
}

for(let i=1; i<=50; i++){
  obtenerUrlsPokemon(i)
}

</script>

<template>
  <div>
    <div id="pokemones">
      <div v-for="pokemon in pokemones" :key="pokemon.id">
        <h1>{{pokemon.id}}</h1>
        <img :src="pokemon.imagen" alt="">
      </div>
    </div>
    <detalle></detalle>
  </div>
</template>

<style scoped>
.pokemones{
  display: flex;
  flex-wrap: wrap;
}
</style>