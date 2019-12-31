<script>
  import { onMount } from "svelte";
  import Select from "svelte-select";

  let cities = [];
  let selectedCity = "";
  let weather = undefined;

  // Helpers for select form
  const getOptionLabel = option => option.label;
  const getSelectionLabel = option => option.label;

  // Triggers a city search based on input text
  function searchCities(search) {
    return new Promise((resolve, reject) => {
      setTimeout(function() {
        resolve(
          cities.filter(city => city.value.startsWith(search.toLowerCase()))
        );
      }, 100);
    });
  }

  // Calls API: get current weather for selected city
  async function callApi(city) {
    const response = await fetch(
      `https://api.weatherbit.io/v2.0/current?key=d61b050238b54f99ab1f0b90194387ea&city=${city}`
    );
    return await response.json();
  }

  // City selection - triggers search
  async function handleSelect(selection) {
    selectedCity = selection.detail.label;
    const response = await callApi(selectedCity);
    console.log(response.data[0]);
    weather = response.data[0];
  }

  // Clear city input field
  function handleClear() {
    selectedCity = "";
  }

  // Load available cities from JSON on component mount
  onMount(async () => {
    const response = await fetch("./cities.json");
    const data = await response.json();
    cities = data.map(city => {
      return {
        value: city.city_name.toLowerCase(),
        label: city.city_name + ", " + city.country_code
      };
    });
  });
</script>

<main>
  <h1>Sweather</h1>
  <div id="city-search-box">
    <Select
      loadOptions="{searchCities}"
      placeholder="Enter a city name..."
      {getOptionLabel}
      {getSelectionLabel}
      on:select="{handleSelect}"
      on:clear="{handleClear}"
    ></Select>
  </div>
  {#if weather != undefined}
  <div id="weather-container">
    <img src="../icons/{weather.weather.icon}.png" alt="" /><br />
    {weather.weather.description}
  </div>
  {/if}
</main>

<style>
  body {
    margin: 0px;
    text-align: center;
  }
  main {
    margin: auto;
    padding: 1em;
    text-align: center;
    max-width: 480px;
    /*background-image: url("../bg.jpg");*/
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  #city-search-box {
    text-align: left;
    margin-bottom: 20px;
  }
</style>
