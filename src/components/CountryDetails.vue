<template>
  <div class="container d-flex flex-column justify-center">
    <!-- <img
      style="width: 300px"
      :src="`https://flagcdn.com/w320/${alpha2Code.toLowerCase()}.png`"
      alt=""
    /> -->
    <ul class="list-group list-group-flush">
      <li class="list-group-item">
        <h3>Name:</h3>
        <p>{{ name }}</p>
      </li>
      <li class="list-group-item">
        <h3>Capital:</h3>
        <p>{{ capital }}</p>
      </li>
      <li class="list-group-item">
        <h3>Area:</h3>
        <p>{{ area }}</p>
      </li>
      <li class="list-group-item">
        <h3>Borders:</h3>
        <p v-if="borders.length === 0">
          This country has no borders<br />
          Maybe is island...
        </p>
        <p v-else v-for="(border, index) in borders" :key="index">
          <router-link :to="`/list/${border}`">{{ border }}</router-link>
        </p>
      </li>
    </ul>

    <!-- Using an Object -->
    <!-- <ul class="list-group list-group-flush">
      <li class="list-group-item">
        <h3>Name:</h3>
        <p>{{ countryInfo.name.common }}</p>
      </li>
      <li class="list-group-item">
        <h3>Capital:</h3>
        <p>{{ countryInfo.capital[0] }}</p>
      </li>
      <li class="list-group-item">
        <h3>Area:</h3>
        <p>{{ countryInfo.area }}</p>
      </li>
      <li class="list-group-item">
        <h3>Borders:</h3>
        <p v-if="countryInfo.borders.length === 0">
          This country has no borders<br />
          Maybe is island...
        </p>
        <p v-else v-for="border in countryInfo.borders">
          <router-link :to="`/list/${border}`">{{ border }}</router-link>
        </p>
      </li>
    </ul> -->
  </div>
</template>

<script setup>
//imports
import { ref, computed, onMounted, watch } from "vue";
import { useRoute, useRouter } from "vue-router";

//Declarations
const name = ref("");
const capital = ref("");
const alphacode = ref("");
const area = ref("");
const borders = ref([]);
const alpha2Code = ref("");
const countryInfo = ref({});
const route = useRoute();

//function and arrow-functions declarations
const getCountryByAlphaCode = async () => {
  const alphacode = route.params.alpha3Code;
  const response = await fetch(
    `https://ih-countries-api.herokuapp.com/countries/${alphacode}`
  );
  const finalResponse = await response.json();
  console.log(finalResponse);

  name.value = finalResponse.name.common;
  capital.value = finalResponse.capital[0];
  area.value = finalResponse.area;
  borders.value = finalResponse.borders;
  alpha2Code.value = finalResponse.alpha2Code;

  // send as an array
  const countryInfo = ref(finalResponse);
  return { name, capital, area, borders, alpha2Code, countryInfo };
};

//Remember, we can use another lifecycle methods while using setup
const countryCode = computed(function () {
  return route.params.alpha3Code;
});

//add watcher to countrycode
watch(countryCode, (newCountryCode) => {
  getCountryByAlphaCode();
});
</script>

<style></style>

<!-- 
:src="`https://flagpedia.net/data/flags/icon/72x54/${alpha2Code.toLowerCase()}.png`" -->
