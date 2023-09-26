<script setup>
import detalle from "./components/detalle.vue";
import axios from "axios";
import { ref, computed } from "vue";

import imgPokeApi from "./assets/pokeapi.png";
import imgFiltrar from "./assets/filtrar.png";
import imgLupa from "./assets/lupa.png";

const componenteBuscar = ref(false)
const componente = ref(true);
const pokemones = ref([]);
const estado = ref("pokemones")
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

async function obtenerUrlsPokemon(i) {
  const pokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}/`);
  console.log(pokemon);
  pokemones.value.push({
    id: pokemon.data.id,
    imagen: pokemon.data.sprites.other["official-artwork"].front_default,
    name: pokemon.data.name,
    altura: pokemon.data.height,
    peso: pokemon.data.weight,
    estadisticas: pokemon.data.stats.map((e) => {
      return { name: e.stat.name, cant: e.base_stat };
    }),
    tipos: pokemon.data.types.map((e) => e.type.name),
  });
}

let cant = 1
let limite = 50

function imprimirPokemones() {
  console.log(cant);
  for (cant; cant <= limite; cant++) {
    obtenerUrlsPokemon(cant);
  }
  limite += 50
  console.log(pokemones.value);
}

document.addEventListener("DOMContentLoaded", imprimirPokemones())

const pokemon = ref({});

function verDetalle(poke) {
  pokemon.value = poke;
  componente.value = !componente.value;
}

const txtBuscar = ref("")
const pokemonBuscado = ref({})

function buscar() {
  pokemonBuscado.value = pokemones.value.find(s => s.name == txtBuscar.value)
  componenteBuscar.value = true
}

const filtroTipos = ref([])
const pokemonesFiltrados = ref([])

function filtrar() {
  
  pokemones.value.forEach((p, i) => {
    filtroTipos.value.forEach(f => {
      if(p.tipos.includes(f)) {
        pokemonesFiltrados.value.push(p)
        return
      }

      return
      
    })
  })
  estado.value = "busqueda"
}

const arrFunciones = {
  pokemones: pokemones.value,
  busqueda: pokemonesFiltrados.value
}

const listado = computed(()=>{
  console.log(arrFunciones[estado.value])
})

</script>

<template>
  <div>
    <h1>{{listado()}}</h1>
    <div id="pokemones" v-if="componente">
      <div>
        <nav class="navbar bg-body-tertiary">
          <div class="container-fluid">
            <img :src="imgPokeApi" alt="" />

            <div id="contBuscar">
              <div id="contCuadroBuscar">
                <img :src="imgLupa" alt="" />
                <input class="form-control me-2" type="search" placeholder="Buscar nombre de pokemon" aria-label="Search"
                  v-model="txtBuscar" />
              </div>

              <button class="btn btn-outline-success" @click="buscar()">Search</button>
            </div>
          </div>
        </nav>
      </div>

      <button id="btnFiltrar" data-bs-toggle="collapse" data-bs-target="#collapseExample" aria-expanded="false"
        aria-controls="collapseExample">
        <img :src="imgFiltrar" alt="" />
        <h4>Filtrar</h4>
      </button>

      <div class="collapse" id="collapseExample">
        <div class="card card-body" id="contFiltros">
          <div v-for="(value, key, i) in coloresTipo" :key="i">
            <label for="" class="tipoCheckbox"><input type="checkbox" v-model="filtroTipos" :value="key"
                @change="filtrar()"> {{ key
                }}</label>
          </div>
        </div>

      </div>


      <div v-if="componenteBuscar">
        <div class="card" style="width: 18rem" @click='verDetalle(pokemonBuscado)'>
          <img :src="pokemonBuscado.imagen" alt="" />
          <div class="card-body">
            <h5 class="card-text">N°{{ pokemonBuscado.id }}</h5>
            <h2 class="card-title">{{ pokemonBuscado.name }}</h2>
            <div class="tipos">
              <div v-for="(tipo, i) in pokemonBuscado.tipos" :key="i" :style="'background-color: ' + coloresTipo[tipo]"
                class="tipo">
                <p>{{ tipo }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="!componenteBuscar">
        <div id="contPokemones">
          <div v-for="(pokemon, i) in listado()" :key="i" @click="verDetalle(pokemon)">


            <div class="card tarjetas" style="width: 18rem">
              <img :src="pokemon.imagen" alt="" />
              <div class="card-body">
                <h5 class="card-text">N°{{ pokemon.id }}</h5>
                <h2 class="card-title">{{ pokemon.name }}</h2>
                <div class="tipos">
                  <div v-for="(tipo, i) in pokemon.tipos" :key="i" :style="'background-color: ' + coloresTipo[tipo]"
                    class="tipo">
                    <p>{{ tipo }}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>

        </div>

        <button @click="imprimirPokemones()">Mostrar más</button>
      </div>


    </div>

    <detalle v-if="!componente" :="pokemon"></detalle>
  </div>
</template>

<style scoped>
#contBuscar {
  display: flex;
  margin: 50px 0;
  align-items: center;
}

#contBuscar>button {
  width: 100px;
  height: 40px;
  border-radius: 25px;
}

#contCuadroBuscar {
  display: flex;
  border: 1px solid black;
  border-radius: 30px;
  padding: 5px 15px;
  margin-right: 20px;
  height: 45px;
  background-color: white;
}

#contCuadroBuscar>img {
  width: 35px;
}

#contCuadroBuscar>input {
  width: 25vw;
  border: 0;
}

.containerfluid {
  justify-content: space-between;
}

#btnFiltrar {
  display: flex;
  border: none;
  background-color: white;
  align-items: center;
  padding: 10px;
}

#btnFiltrar>img {
  width: 20px;
  margin-right: 5px;
}

#btnFiltrar>h4 {
  margin: 0;
  background-color: white;
}

#contPokemones {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  padding: 0 5vw;
}

.tarjetas {
  height: 450px;
  margin: 10px;
  background-color: #f2f2f2;
}

.card-body {
  background-color: white;
}

.tipos {
  display: flex;
  justify-content: space-evenly;
}

.tipo {
  padding: 5px 10px 5px 10px;
  border-radius: 10px;
}

.tipo>* {
  margin: 0;
  padding: 0;
  color: white;
}

#contFiltros {
  display: flex;
  flex-direction: row;
}

.tipoCheckbox {
  margin-right: 10px;
}
</style>
