<script setup>
import axios from "axios";
import { ref } from "vue";

const pokemon = defineProps({
  id: {
    type: Number,
  },
  name: {
    type: String,
  },
  tipos: {
    type: Array,
  },
  altura: {
    type: String,
  },
  peso: {
    type: String,
  },
  imagen: {
    type: String
  },
  estadisticas:{
    type: Array
  }
});

console.log(pokemon);

/* const c = ref(false)
const pokemon = ref({}) */

/* async function obtenerUrlsPokemon() {
  // let r = await axios.get("https://pokeapi.co/api/v2/pokemon?limit=50&offset=0")
  const pokemon = await axios.get("https://pokeapi.co/api/v2/pokemon/3/")
  console.log(pokemon)
  pokemon.value = {
    id: pokemon.data.id,
    imagen: pokemon.data.sprites.other['official-artwork'].front_shiny,
    name: pokemon.data.name,
    altura: pokemon.data.height,
    peso: pokemon.data.weight,
    estadisticas: pokemon.data.stats.map((e) => {
      return { name: e.stat.name, cant: e.base_stat }
    }),
    tipos: pokemon.data.types.map(e => e.type.name)
  }

  c.value = true
} */

const concatenar = (cant) => {
  let a = "width: ";
  a += cant;
  a += "%;";
  return a;
};
</script>

<template>
  <div>
    <!-- <button @click="obtenerUrlsPokemon()">Cargar</button> -->
    <div>
      <div>
        <div>
          <h1>#{{ pokemon.id }}</h1>
          <h2>{{ pokemon.name }}</h2>
          <div>
            <div v-for="(tipo, index) in pokemon.tipos" :key="index">
              {{ tipo }}
            </div>
          </div>
          <div>
            <div>
              <b>Altura</b>
              <p>{{ pokemon.altura }}</p>
            </div>
            <div>
              <b>Peso</b>
              <p>{{ pokemon.peso }}KG</p>
            </div>
          </div>
        </div>

        <img :src="pokemon.imagen" alt="" />
      </div>

      <div>
        <h2>Estadísticas</h2>
        <div>
          <div v-for="(stat, index) in pokemon.estadisticas" :key="index">
            <p>{{ stat.name }}</p>
            <div style="width: 100%; height: 20px; background-color: white">
              <div :style="concatenar(stat.cant)" class="porcentaje"></div>
            </div>
            <p>{{ stat.cant }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.porcentaje {
  height: 20px;
  background-color: red;
}
</style>
