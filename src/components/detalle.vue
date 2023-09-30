<script setup>
import axios from "axios";
import { ref } from "vue";

const coloresTipo = {
  normal: "#A8A878",
  fire: "#F08030",
  water: "#6890F0",
  grass: "#78C850",
  electric: "#F8D030",
  ice: "#98D8D8",
  fighting: "#C03028",
  poison: "#A040A0",
  ground: "#E0C068",
  flying: "#A890F0",
  psychic: "#F85888",
  bug: "#A8B820",
  rock: "#B8A038",
  ghost: "#705898",
  steel: "#B8B8D0",
  dragon: "#7038F8",
  dark: "#705848",
  fairy: "#EE99AC",
};

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
    type: String,
  },
  estadisticas: {
    type: Array,
  },
});

console.log(pokemon);

const concatenar = (cant) => {
  let a = `width: ${cant}%;`;
  return a;
};

const subBarraColores = {
  "25": "bg-success",
  "50": "bg-info text-dark",
  "75": "bg-warning text-dark",
  "100": "bg-danger"
}

const colorSubBarraProgreso = (cant)=>{
  for(const key in subBarraColores){
    if(cant<parseInt(key)){
      return subBarraColores[key]
    }
  }

  return subBarraColores["100"]
}

const barraColores = {
  "25": "Success example",
  "50": "Info example",
  "75": "Warning example",
  "100": "Danger example"
}

const colorBarraProgreso = (cant)=>{
  for(const key in barraColores){
    if(cant<parseInt(key)){
      return barraColores[key]
    }
  }

  return barraColores["100"]
}

function primeraLetraMayuscula(cadena) {
  return cadena.charAt(0).toUpperCase() + cadena.slice(1);
}
</script>

<template>
  <div>
    <div id="bodyDetalle">
      <div id="header">
        <div id="contInfo">
          <h1>#{{ pokemon.id }}</h1>
          <h2>{{ primeraLetraMayuscula(pokemon.name) }}</h2>
          <div class="tipos">
            <div
              v-for="(tipo, i) in pokemon.tipos"
              :key="i"
              :style="'background-color: ' + coloresTipo[tipo]"
              class="tipo"
            ><p>{{ tipo }}</p></div>
          </div>
          <div id="contMedidas">
            <div>
              <h5>Altura</h5>
              <p>{{ pokemon.altura }}</p>
            </div>
            <div>
              <h5>Peso</h5>
              <p>{{ pokemon.peso }}KG</p>
            </div>
          </div>
        </div>

        <img :src="pokemon.imagen" alt="" />
      </div>

      <div id="contEstadisticas">
        <h2>Estadísticas</h2>
        <div>
          <div v-for="(stat, index) in pokemon.estadisticas" :key="index" class="barraProgreso">
            <p>{{ primeraLetraMayuscula(stat.name) }}</p>
            <div class="progress" role="progressbar" :aria-label="colorBarraProgreso(stat.cant)" :aria-valuenow="stat.cant" aria-valuemin="0" aria-valuemax="100" style="background-color: black;">
              <div :style="concatenar(stat.cant)" class="progress-bar" :class="colorSubBarraProgreso(stat.cant)">{{ stat.cant }}%</div>
            </div>
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

.tipos {
  display: flex;
}

#contInfo {
  width: 50%;
}

#contInfo > h1 {
  font-size: 15vw;
  color: #83c4be
}

#contInfo > h2 {
  font-size: 5vw;
  text-align: end;
  
}

#contMedidas {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
}

#header {
  display: flex;
  justify-content: center;
  align-items: center;
}

#header > img {
  height: 30vw;
}

#contEstadisticas {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
  margin-top: 20px;
}

#contEstadisticas>h2 {
  /* width: 25%; */
  margin: 20px;
  text-align: center;
}

#contEstadisticas>div{
  width: 75%;
}

.barraProgreso{
  display: flex;
  flex-wrap: wrap;
 align-items: center;
 margin: 10px 0;
}

.barraProgreso>p{
  flex: 1;
  margin: 0;
}

#tipo{
margin-right: 10px;
}

#bodyDetalle{
  padding: 0 5vw;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  min-height: 100vh;
}

.progress{
  width: 75%;
}
</style>
