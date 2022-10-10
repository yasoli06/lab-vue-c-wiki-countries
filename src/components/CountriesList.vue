<template>
  <div class="container">
    <h1 class="text-center mb-5">Country List</h1>
    <!-- row wrapper div de bootstrap -->
    <div class="row" v-if="this.countries">
      <div class="col-5">
        <div class="list-group">
          <router-link
            v-for="(country, index) in countries"
            :key="index"
            :to="`/list/${country.alpha3Code}`"
            class="list-group-item list-group-item-action d-flex flex-column justify-content-center"
          >
            <img
              class="flag"
              :src="`https://flagpedia.net/data/flags/icon/72x54/${country.alpha2Code.toLowerCase()}.png`"
              alt=""
            />
            <p>
              {{ country.name.common }}
            </p>
          </router-link>
        </div>
      </div>
      <div class="col-7">
        <router-view />
      </div>
    </div>
    <div class="row" v-else>
      <div class="d-flex justify-content-center">
        <Spinner text="Loading Countries..." />
      </div>
    </div>
  </div>
</template>

<script setup>
import Spinner from "../components/Spinner.vue";
import { ref, computed, onMounted, defineComponent } from "vue";
import { useRoute, useRouter } from "vue-router";

//Declare countries variable which will be used in the template section
const countries = ref(null);

//Create an async function which will replace the content of countries.value. Note that we have to use .value because we have declared countries as a ref.
async function countriesAssign() {
  //As long there is an async state, we have to use await. We use it at this point because we want the function to wait until db response.
  const response = await fetch(
    "https://ih-countries-api.herokuapp.com/countries"
  );
  const finalResponse = await response.json();
  //console.log(finalResponse); //VERIFICAMOS MEDIANTES UNA LLAMADA A CONSOLA QUE RECIBIMOS LOS DATOS
  //We add in order the elements of finalresponse to countries.value
  countries.value = finalResponse.sort((a, b) => {
    return a.name.common.localeCompare(b.name.common);
  });
}

countriesAssign();
</script>

<style>
.flag {
  width: 100px;
  height: 80px;
}
</style>
