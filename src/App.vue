<script setup>
import detalle from "./components/detalle.vue";
import axios from "axios";
import { ref, computed } from "vue";

import imgPokeApi from "./assets/pokeapi.png";
import imgFiltrar from "./assets/filtrar.png";
import imgLupa from "./assets/lupa.png";

const componenteBuscar = ref(false);
const componente = ref(true);

/* const arrFunciones = ref({
  pokemones: [],
  busqueda: [],
}); */
const estado = ref("pokemones");
const data = ref({
  pokemones: {
    data: [],
    cant: 0,
    limite: 50,
  },
  filtro: {
    data: [],
    cant: 0,
    limite: 50,
  },
});
const links = ref({
  pokemones: (l, o) =>
    `https://pokeapi.co/api/v2/pokemon?limit=${l}&offset=${o}`,
  filtro: () => `https://pokeapi.co/api/v2/type`,
  nombrePokemon: "https://pokeapi.co/api/v2/pokemon",
});

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
const tipos = ref([]);
const filtroTipos = ref([]);

const pokemonesFiltrados = ref([]); /////////////////


obtenerPokemones(
  links.value["pokemones"](
    data.value[estado.value].limite,
    data.value[estado.value].cant
  )
);
obtenerTipos();


async function obtenerPokemones(url) {
  if (filtroTipos.value.length <= 0) {
    estado.value = "pokemones";
  } else estado.value = "filtro";

  console.log(
    "p2",
    links.value[estado.value](
      data.value[estado.value].limite,
      data.value[estado.value].cant
    )
  );
  const dataPokes = await axios.get(url);
  const axiosGet = {
    pokemones: dataPokes.data.results,
    filtro: dataPokes.data.pokemon,
  };
  console.log(dataPokes);
  console.log(data.value[estado.value].cant);

  console.log(axiosGet[estado.value]);

  const promesas = [];
  axiosGet[estado.value].forEach(async (p) => {
    const urlAxios = {
      pokemones: p.url,
      filtro: p.pokemon?.url
    };
    promesas.push(axios.get(urlAxios[estado.value]));
  });

  const resultados = await Promise.all(promesas);

  for (const [i, pokemon] of resultados.entries()) {
    const divisor = filtroTipos.value.length > 0 || !filtroTipos.value % 2 == 0 ? filtroTipos.value.length : filtroTipos.value.length + 1
    console.log("d", data.value[estado.value].limite / divisor);
    if (i >= (data.value[estado.value].limite / divisor)) {
      break
    }

    if (
      data.value[estado.value].data.find((p) => p.id === pokemon.data.id) ===
      undefined
    ) {
      data.value[estado.value].data.push({
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
  };

  console.log("cant3", data.value[estado.value].limite);
}

const dataBuscar = ref({
  txtBuscar: "",
});
const pokemonBuscado = ref({});

async function buscar() {
  if (dataBuscar.value.txtBuscar === "") {
    componenteBuscar.value = false
    return
  }

  const pokemon = await axios.get(
    links.value.nombrePokemon + "/" + dataBuscar.value.txtBuscar
  );
  if (!pokemon) {
    console.log("no encontrado");
    return;
  }

  pokemonBuscado.value = {
    id: pokemon.data.id,
    imagen: pokemon.data.sprites.other["official-artwork"].front_default,
    name: pokemon.data.name,
    altura: pokemon.data.height,
    peso: pokemon.data.weight,
    estadisticas: pokemon.data.stats.map((e) => {
      return { name: e.stat.name, cant: e.base_stat };
    }),
    tipos: pokemon.data.types.map((e) => e.type.name),
  };
  componenteBuscar.value = true;
}

async function mostrarMas() {
  data.value[estado.value].limite += 50;
  console.log("cant", data.value[estado.value].cant);
  console.log(
    "p",
    links.value[estado.value](
      data.value[estado.value].limite,
      data.value[estado.value].cant
    )
  );
  obtenerPokemones(
    links.value[estado.value](
      data.value[estado.value].limite,
      data.value[estado.value].cant
    )
  );
}

async function filtrar() {
  data.value.filtro.cant = 1;
  filtroTipos.value.forEach(async (url) => {
    await obtenerPokemones(url);
  });
}

const desactivarInput = ref();
function quitarFiltros() {
  filtroTipos.value = [];
  if (filtroTipos.value.length <= 0) estado.value = "pokemones";

  desactivarInput.value = false;
}

async function obtenerUrlsPokemon(i) {
  const pokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}/`);
  console.log(pokemon);
  arrFunciones.value["pokemones"].push({
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

let cant = 1;
let limite = 50;

function imprimirPokemones() {
  console.log(cant);
  for (cant; cant <= limite; cant++) {
    obtenerUrlsPokemon(cant);
  }
  limite += 50;
  console.log(arrFunciones.value["pokemones"]);
}

const pokemon = ref({});

function verDetalle(poke) {
  pokemon.value = poke;
  componente.value = !componente.value;
}

/* function filtrar() {
  arrFunciones.value["pokemones"].forEach((p, i) => {
    filtroTipos.value.forEach((f) => {
      if (p.tipos.includes(f)) {
        pokemonesFiltrados.value.push(p);
        return;
      }

      pokemonesFiltrados.value.forEach((pF, i) => {
        if (!pF.tipos.includes(f)) pokemonesFiltrados.value.splice(i, 1);

        return;
      });

      return;
    });
  });
  estado.value = "busqueda";
} */

async function obtenerTipos() {
  const t = await axios.get("https://pokeapi.co/api/v2/type/");

  tipos.value = t.data.results;
}
let cantFiltro = 0;
let limiteFiltro = 50;

async function filtrarTipos(url) {
  if (filtroTipos.value.length <= 0) {
    estado.value = "pokemones";
    return;
  }
  estado.value = "busqueda";
  const tipo = await axios.get(url);

  for (cantFiltro; cantFiltro < limiteFiltro; cantFiltro++) {
    const pokemon = await axios.get(tipo.data.pokemon[cantFiltro].pokemon.url);
    // console.log(arrFunciones.value[estado.])
    arrFunciones.value[estado.value].push({
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
                <input class="form-control me-2" type="search" placeholder="Buscar nombre de pokemon" aria-label="Search"
                  v-model="dataBuscar.txtBuscar" @keyup.enter="buscar()" @change="buscar()" />
              </div>

              <button class="btn btn-outline-success" @click="buscar()">
                Buscar
              </button>
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
          <div v-for="(t, i) in tipos" :key="i">
            <label for="" class="tipoCheckbox"><input type="checkbox" v-model="filtroTipos" :value="t.url"
                class="inputCheckbox" :checked="desactivarInput" />
              {{ t.name }}</label>
          </div>
        </div>
        <button @click="filtrar()" class="btn btn-outline-secondary">Filtrar</button>
        <button v-if="filtroTipos.length > 0" @click="quitarFiltros()" class="btn btn-outline-warning">
          Quitar filtros
        </button>
      </div>

      <div v-if="componenteBuscar">
        <div class="card" style="width: 18rem" @click="verDetalle(pokemonBuscado)">
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
          <div v-for="(pokemon, i) in data[estado].data" :key="i" @click="verDetalle(pokemon)">
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

        <div id="btnMostrarMas">
          <button @click="mostrarMas()" class="btn btn-outline-secondary" >Mostrar más</button>
        </div>
      </div>
    </div>

    <detalle v-if="!componente" :="pokemon"></detalle>
  </div>
</template>

<style scoped>
#btnMostrarMas{
  width:100%;
  display: flex;
  justify-content: center;
  margin-bottom: 10px;
}

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





#contFiltros {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}

.tipoCheckbox {
  margin-right: 10px;
}
</style>
