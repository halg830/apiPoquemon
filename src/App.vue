<script setup>
import detalle from "./components/detalle.vue";
import axios from "axios";
import { ref } from "vue";

import imgPokeApi from "./assets/pokeapi.png";
import imgFiltrar from "./assets/filtrar.png";
import imgLupa from "./assets/lupa.png";

const componente = ref(true);
const pokemones = ref([]);
const coloresTipo = {
  grass: "green",
  poison: "violet",
  fire: "orange",
};

async function obtenerUrlsPokemon(i) {
  const pokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}/`);
  console.log(pokemon);
  pokemones.value.push({
    id: pokemon.data.id,
    imagen: pokemon.data.sprites.other["official-artwork"].front_shiny,
    name: pokemon.data.name,
    altura: pokemon.data.height,
    peso: pokemon.data.weight,
    estadisticas: pokemon.data.stats.map((e) => {
      return { name: e.stat.name, cant: e.base_stat };
    }),
    tipos: pokemon.data.types.map((e) => e.type.name),
  });
}

for (let i = 1; i <= 50; i++) {
  obtenerUrlsPokemon(i);
}

const pokemon = ref({});

function verDetalle(poke) {
  pokemon.value = poke;
  componente.value = !componente.value;
}
</script>

<template>
  <div>
    <div id="pokemones" v-if="componente">
      <div>
        <nav class="navbar bg-body-tertiary">
          <div class="container-fluid">
            <img :src="imgPokeApi" alt="" />

            <div id="contBuscar">
              <div id="contCuadroBuscar">
                <img :src="imgLupa" alt="" />
                <input
                  class="form-control me-2"
                  type="search"
                  placeholder="Buscar nombre de pokemon"
                  aria-label="Search"
                />
              </div>

              <button class="btn btn-outline-success">Search</button>
            </div>
          </div>
        </nav>
      </div>

      <button id="btnFiltrar">
        <img :src="imgFiltrar" alt="" />
        <h4>Filtrar</h4>
      </button>

      <div
        v-for="(pokemon, i) in pokemones"
        :key="i"
        @click="verDetalle(pokemon)"
      >
        <div class="card" style="width: 18rem">
          <img :src="pokemon.imagen" alt="" />
          <div class="card-body">
            <h5 class="card-text">N°{{ pokemon.id }}</h5>
            <h2 class="card-title">{{ pokemon.name }}</h2>
            <div>
              <div
                v-for="(tipo, i) in pokemon.tipos"
                :key="i"
                :style="'background-color: ' + coloresTipo[tipo]"
              >
                <p>{{ tipo }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <detalle v-if="!componente" :="pokemon"></detalle>
  </div>
</template>

<style scoped>
#pokemones {
  padding: 0 5vw;
}

#contBuscar {
  display: flex;
  margin: 50px 0;
  align-items: center;
}

#contBuscar > button {
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
}

#contCuadroBuscar > img {
  width: 35px;
}

#contCuadroBuscar > input {
  width: 25vw;
  border: 0;
}

.containerfluid {
  justify-content: space-between;
}

#btnFiltrar {
  display: flex;
  border: none;
}

#btnFiltrar > img {
  width: 20px;
  margin-right: 5px;
}

#btnFiltrar > h4 {
  margin: 0;
}
</style>
