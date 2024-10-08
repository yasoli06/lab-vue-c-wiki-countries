<script setup>
import { ref, watch } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const routeParam = route.params.alpha3Code;

const countries = ref([]);
const country = ref(null);
const capital = ref('');
const area = ref('');
const img = ref('');
const borders = ref([]);

const a3cToName = (a3c) => {
  const country = countries.value.find((c) => c.alpha3Code === a3c);
  return country ? country.name.common: 'Unknown';
};

const countryDetails = async(alpha3Code) => {
    try {
        const response = await fetch('https://ih-countries-api.herokuapp.com/countries/' + alpha3Code);
        const data = await response.json();
        country.value = data;     

        capital.value = country.value.capital || 'No capital';
    area.value = country.value.area || 'Unknown';
    img.value =  "https://flagpedia.net/data/flags/icon/72x54/" + country.value.alpha2Code.toLowerCase() + ".png";
    borders.value = country.value.borders|| [];

    if (countries.value.length === 0) {
      const responseCountries = await fetch("https://ih-countries-api.herokuapp.com/countries");
      countries.value = await responseCountries.json();
    }
  } catch (error) {
    console.log ("Error fetching country details:", error);
  }
};
countryDetails(routeParam);

watch(
  () => route.params.alpha3Code, 
  (newAlpha3Code) => {
    countryDetails(newAlpha3Code);
  }
);
</script>


<template>
 <div class="container-fluid" v-if="country">
    <div class="row">
      <div
        class="main-content col-md-5 p-2 position-fixed"
        style="height: 750px"
      >
        <div v-if="country" class="text-center">
          <img :src="img" class="mt-5" alt="country flag" />
          <h2 class="mt-4">{{ country.name.common }}</h2>
          <div class="block">
            <p>Capital</p>
            <p>{{ capital }}</p>
          </div>
          <div class="separator"></div>
          <div class="block">
            <p>Area</p>
            <p>{{ area }} km<sup>2</sup></p>
          </div>
          <div class="separator"></div>
          <div v-if="borders && borders.length > 0" class="block2">
            <p>Borders</p>
            <ul>
              <li v-for="border in borders" :key="border">
                <router-link :to="'/list/' + border">{{ a3cToName(border) }}</router-link>
              </li>
            </ul>
            <div class="separator"></div>
          </div>
          <div v-else>
            <p>Borders</p>
            <p>Does not have any</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.position-fixed {
  top: 20px;
  right: 8.33%;
}

img {
  height: 100px;
}

.separator {
  height: 2px;
  background-color: lightgrey;
  border-radius: 1%;
}

.block {
  display: flex;
  justify-content: space-around;
  align-content: center;
  align-items: center;
  margin-top: 5px;
  font-weight: 500;
}

.block2 {
  display: flex;
  justify-content: space-around;
  align-content: center;
  align-items: center;
  margin-top: 5px;
  font-weight: 500;
  width: 140%;
}

li{
    list-style-type: none;
}
</style>