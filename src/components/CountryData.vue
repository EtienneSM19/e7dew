<script setup>
const props = defineProps({data: Object,}); 
</script>

<template>
  <template v-if="data">
    <div class="data">
      <span><b>Nombre:</b> {{ data.name }}</span>
      <span v-if="data.population"><b>Población:</b> {{ data.population }}</span>
      <span v-if="data.pib"><b>PIB:</b> {{ data.pib }}</span>
      <span v-if="data.area"><b>Área:</b> {{ data.area }}</span>
      <span v-if="data.income"><b>Renta:</b> {{ data.income }}</span>
      <span v-if="data.population && data.pib"><b>PIB/Habitante</b> {{ (data.pib / data.population).toFixed(2) }}</span>
      <button class="remove" @click="$emit('removeCountry', data.code)">Eliminar</button>
    </div>
  </template>
  <div v-else>
    <span>No hay datos que mostrar</span>
  </div>
</template>

<style>
.data {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  margin: 0.5rem;
}

.remove {
  background-color: red;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 0.5rem;
  cursor: pointer;
  display: inline-block;
}
</style>