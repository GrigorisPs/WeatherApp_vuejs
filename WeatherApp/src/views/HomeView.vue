<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">

      <input v-model="searchQuery" @input="getSeatchResults" type="text" placeholder="Search for a city or state" class=" py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none
      focus:shadow-[0px_1px_0_0_#004E71]">
      
      <ul v-if="mapboxResults" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">

        <!-- In case of Data not found -->
        <p v-if="searchError"> Sorry, something went wrong, please try again.</p>

        <!-- In case of Server error or no matches -->
        <p v-if="!serverError && mapboxResults.length === 0">No Results match your query, try a different term </p>

        <!-- Else statement -->
        <template v-else>
          <li v-for="searchResult in mapboxResults " :key="searchResult.id" class="py-2 cursor-pointer">
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios'

const mapboxAPIKey = 'pk.eyJ1IjoiZ3JpZ29yaXNwcyIsImEiOiJjbG5oZnZncW4wcG5qMmlydXZqY2phN2cwIn0.WiSijdAPnlMWS0hRdrJRmQ'
const searchQuery = ref("")
const queryTimeout = ref(null)
const mapboxResults = ref(null)
const searchError = ref(null)

const getSeatchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxResults.value = result.data.features;
        console.log(mapboxResults.value)

      } catch {
        seartchError.value = true

      }
      return;

    }
    mapboxResults.value = null
  }, 300);
}

</script>