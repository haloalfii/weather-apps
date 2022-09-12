

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        @input="getSearchResult"
        v-model="city"
        type="text"
        placeholder="Search City"
        class="
          py-2
          px-1
          w-full
          bg-transparent
          border-b
          focus:border-weather-secondary
          focus:outline-none
          focus:shadow-[0px_1px_0_0#004e71]
        "
      />
      <ul
        class="absolute text-white w-full py-2 px-1 top-[66px]"
        :class="{ ' bg-weather-secondary shadow-md': city !== '' }"
      >
        <p class="py-2" v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p
          class="py-2"
          v-if="!searchError && city !== '' && searchResult.length === 0"
        >
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="data in searchResult"
            :key="data.id"
            class="py-2 cursor-pointer"
            @click="previewCity(data)"
          >
            {{ data.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script>
import axios from "axios";

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";

export default {
  data() {
    return {
      city: "",
      queryTimeout: null,
      searchResult: [],
      searchError: 0,
    };
  },

  methods: {
    async getSearchResult() {
      if (this.city !== "") {
        try {
          const res = await axios.get(
            `https://api.mapbox.com/geocoding/v5/mapbox.places/${this.city}.json?access_token=${mapboxAPIKey}&types=place`
          );

          setTimeout(() => {
            this.searchResult = res.data.features;
          }, 300);
        } catch (error) {
          this.searchError = 1;
        }
      } else {
        this.searchResult = [];
        this.searchError = 0;
      }
    },

    previewCity(data) {
      const [city, state] = data.place_name.split(", ");
      this.$router.push({
        name: "cityView",
        params: { state: state.replaceAll(" ", ""), city: city },
        query: {
          lat: data.geometry.coordinates[1],
          lng: data.geometry.coordinates[0],
          priview: true,
        },
      });
    },
  },
};
</script>