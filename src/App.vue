<script setup>
import detalle from './components/detalle.vue'
import axios from 'axios';
import {ref} from 'vue'
const pokemones = ref([])
const coloresTipo = {
  grass: "green",
  poison: "violet",
  fire: "orange"
}

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

function verDetalle(pokemon){
  
}
</script>

<template>
  <div>
    <div id="pokemones" v-if="app">
      <div v-for="(pokemon, i) in pokemones" :key="i" @click="verDetalle(pokemon)">
        <img :src="pokemon.imagen" alt="">
        <h1>N°{{pokemon.id}}</h1>
        <h1>{{ pokemon.name }}</h1>
        <div>
          <div v-for="(tipo, i) in pokemon.tipos" :key="i" :style="'background-color: ' + coloresTipo[tipo]">
            <h3>{{ tipo }}</h3>
          </div>
        </div> 
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