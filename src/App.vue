<script setup>
import { onMounted, ref, computed, reactive } from 'vue';
import codes from '../data/codes.json';
import CountryData from './components/CountryData.vue';
import GoogleChart from './components/GoogleChart.vue';

const userName = ref ('Etienne');
const selectedCountries = ref([]);
const selectedCountriesData = reactive({});
const countryNames = ref({});
let countryData = ref(null);
const dataTypes = ['population', 'pib', 'area', 'income'];
const selectedDataType = ref('population');


onMounted(async () => {
  const res = await fetch('http://localhost:3000/api/names');
  countryNames.value = await res.json();
})

function selectCountry(code) {
  if(!selectedCountries.value.includes(code)){
    selectedCountries.value.push(code);
  }
}

const sortedSelectedCountries = computed(() => {
  return selectedCountries.value.slice().sort((a, b) => {
    const nameA = countryNames.value[a]?.toLowerCase() || '';
    const nameB = countryNames.value[b]?.toLowerCase() || '';
    return nameA.localeCompare(nameB);
  });
});

const countryChart = computed(() => {
  const res = [
    ["país", selectedDataType.value],
    ...sortedSelectedCountries.value.map((code) => {
      const country = countryNames.value[code] || code;
      const data = selectedCountriesData[code];
      return [country, data ? data[selectedDataType.value] : 0];
    }),
  ];
  console.log(res);
  return res;
})

function discardCountry(code) {
  selectedCountries.value = selectedCountries.value.filter(c => c !== code);
  countryData.value = null;
}

async function fetchCountryData(code) {
  try {
    const response = await fetch('http://localhost:3000/api/country/' + code);
    countryData.value = await response.json();
    console.log(countryData.value);
    selectedCountriesData[code] = countryData.value;
  } catch (e) {
    console.log('Pais no encontrado');
  }
};

async function removeCountry(code) {
  const res = await fetch('http://localhost:3000/api/country/' + code, {
    method: 'DELETE'
  });
  if(!res.ok){
    console.log('Pais no encontrado');
  }
  selectedCountries.value = selectedCountries.value.filter(c => c !== code);
  countryData.value = null;
}

</script>

<template>
  <div class="parent">
    <div class="header">
      <h1>Datos mundiales</h1>
    </div>
    <div class="name">
      <label for="">
        Inserta tu nombre:
        <input type="text" v-model="userName" class="name"/>
      </label>
      <p>Tu nombre <b>{{ userName }}</b> tiene <b>{{ userName.length }}</b> carácteres</p>
    </div>
    <div class="div-codes">
      <h1>Códigos:</h1>
      <ul>
        <li v-for="code in codes" :key="code" @click="selectCountry(code)">{{ countryNames[code] }}</li>
      </ul>
    </div>
    <div class="selected">
      <h1>Seleccionados ({{ sortedSelectedCountries.length }}):</h1>
      <ul>
        <li v-for="code in sortedSelectedCountries" :key="code">
        <span @click="fetchCountryData(code)">{{ countryNames[code] }}</span>
        <button @click="discardCountry(code)">Desmarcar</button>
      </li>
      </ul>
    </div>
    <div class="country-data">
      <h1>Datos del país</h1>
      <CountryData 
      :data="countryData"
      @removeCountry="removeCountry"/>
    </div>
    <div class="options">
      <label v-for="type in dataTypes" :key="type">
        <input type="radio" :value="type" v-model="selectedDataType" name="type"/>
        {{ type }}
      </label>
    </div>
    <div class="chart">
      <GoogleChart
      :data="countryChart">
      </GoogleChart>
    </div>
  </div>
</template>

<style scoped>
* {
  color: black;
}

.data-item {
  display: grid;
  grid-template-columns: 1fr 2fr;
  margin: 0.5rem;
}

.parent {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: auto repeat(3, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
  height: 100vh;
  margin: -2rem;
}

.header {
  grid-area: 1 / 1 / 2 / 6;
  background-color: aquamarine;
}

.header h1 {
  margin: 2px;
}

.div-codes {
  grid-area: 2 / 1 / 6 / 2;
  background-color: azure;
  overflow-y: auto;
}

.name {
  grid-area: 2 / 2 / 3 / 3;
  background-color: antiquewhite;
}

.selected {
  grid-area: 2 / 5 / 4 / 6;
  background-color: lightyellow;
  overflow-y: auto;
}

.country-data {
  grid-area: 2 / 3 / 3 / 5;
  background-color: lightseagreen;
}

.options {
  grid-area: 3 / 2 / 4 / 5;
  background-color: bisque;
}

.chart {
  grid-area: 4 / 2 / 5 / 6;
  background-color: aqua;
}

ul li {
  text-align: left;
  font-size: 0.9rem;
}

ul li:hover {
  font-weight: bold;
  cursor: pointer
}

ul li button {
  font-size: 0.6rem;
  margin: 2px;
}

h2 {
  font-size: 1.2rem;
}
</style>
