<script setup>
import { ref } from 'vue';

const countries = ref([]);

const fetchCountries = async() => {
    try {
        const response = await fetch('https://ih-countries-api.herokuapp.com/countries');
        if (!response.ok) { 
            throw new Error ('failed to fetch countries');
        }
        const data = await response.json();
        countries.value = data.sort((a,b) => {
            return a.name.common.localeCompare(b.name.common);
        });    
    } catch (error) {
        console.log("error at JSON file:", error);
    } 
};

fetchCountries();
</script>

<template>
    <div class="container">
        <div class="row">
            <div class="col-md-1 bg-white"></div>
                <div class="main-content col-md-4 bg-white overflow-auto text-center">
                    <ul>
                        <li
                        v-for="country in countries"
                        :key="country.alpha3Code">
                    <router-link :to="'/list/' + country.alpha3Code">
                    <div class="border">
                    <img
                        :src="'https://flagpedia.net/data/flags/icon/72x54/' + country.alpha2Code.toLowerCase() + '.png'"
                       alt="flag"
                  class="pt-3">
                    <p>{{ country.name.common }}</p>
                    </div>
        </router-link>
          </li>
        </ul>
    </div>
    </div>
    </div>
    <router-view></router-view>
</template>

<style scoped>
ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  margin-bottom: 15px;
  transition: background-color 0.3s ease, transform 0.2s ease;
}
li:hover {
  transform: scale(1.02);
}
img {
  margin-right: 15px;
  width: 50px;
  height: auto;
  border-radius: 3px;
}
p {
  margin: 0;
  font-size: 16px;
  font-weight: 500;
}

@media (max-width: 768px) {
  .col-5 {
    max-height: 60vh; /* Ajusta la altura máxima para dispositivos pequeños */
  }

  img {
    width: 40px; /* Reduce el tamaño de la imagen en pantallas pequeñas */
  }

  p {
    font-size: 14px; /* Reduce el tamaño del texto en pantallas pequeñas */
  }
}
</style>