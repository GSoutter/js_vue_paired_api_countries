<template>
  <div id="app">
    <h2>Global population: {{totalPopulation(countries)}}</h2>

    <label for="country_select">Select a Country: </label>
    <select id="country_select" v-model="selectedCountry">
      <option disabled value="">Select a country</option>
      <option v-for="country in countries" :value="country">{{country.name}}</option>
    </select>

  <!-- Country detail goes here -->
    <section v-if="(selectedCountry != null)">
      <h4>{{selectedCountry.name}}</h4>
      <img v-bind:src="selectedCountry.flag" alt="Flag of country">
      <ul >
        <li>Total population of neighbouring countries: {{totalPopulation(neighbourCountries)}} </li>
        <li v-for="country in neighbourCountries">{{country.name}}</li>
      </ul>
    </section>

    <button v-on:click="addCountry">Add Country</button>

    <!-- Favourite Countries goes here -->
    <section v-if="(favouriteCountries.length !== 0)" v-for="country in favouriteCountries" :value="country">
      <h4>{{country.name}}</h4>
      <img v-bind:src="country.flag" alt="Flag of country">
    </section>

</div>

</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      countries: [],
      selectedCountry: null,
      favouriteCountries: []
    }
  },
  computed: {
    neighbourCountries: function (){
      const countryNeigh = []
      const countryNeighCodes = this.selectedCountry.borders;
      for (let countryCode of countryNeighCodes) {
        for (let country of this.countries) {
          if (country.alpha3Code === countryCode) {
            countryNeigh.push(country);
            continue;
          }
        }
      }
      return countryNeigh;
    }
  },

  mounted(){
    this.allCountriesAdd()
  },

  methods: {
    allCountriesAdd: function (){
      fetch("https://restcountries.eu/rest/v2/all")
      .then(response => response.json())
      .then(data => this.countries = data)
    },
    addCountry: function (){
      if (this.favouriteCountries.includes(this.selectedCountry) === false) {
        this.favouriteCountries.push(this.selectedCountry);
      }

    },
    totalPopulation: function (countries) {
      return countries.reduce((total, country) => total + country.population, 0)
    }
  }
}
</script>

<style>
.small-flag {
  height: 20px
}

img {
  width: 50px;
}

h4 {
  margin: -1px;
}

</style>
