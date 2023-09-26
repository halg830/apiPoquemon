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

const concatenar = (cant) => {
  let a = "width: ";
  a += cant;
  a += "%;";
  return a;
};
</script>

<template>
  <div>
    <div>
      <div id="header">
        <div id="contInfo">
          <h1>#{{ pokemon.id }}</h1>
          <h2>{{ pokemon.name }}</h2>
          <div class="tipos">
            <div v-for="(tipo, index) in pokemon.tipos" :key="index" >
              <p>{{ tipo }}</p> 
            </div>
          </div>
          <div id="contMedidas">
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

      <div id="contEstadisticas">
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

.tipos{
  display: flex;
}

#contInfo{
  /* display: flex; */
}

#contMedidas{
  display: flex;
}

#header{
  display: flex;
}

#contEstadisticas{
  display: flex;
}
</style>
