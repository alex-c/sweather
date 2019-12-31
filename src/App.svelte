<script>
  import { onMount } from "svelte";
  import Select from "svelte-select";

  let cities = [];
  let selectedCity = "";
  let weather = undefined;
  let forecast = undefined;

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
  async function callApiGetCurrentWeather(city) {
    const response = await fetch(
      `https://api.weatherbit.io/v2.0/current?key=d61b050238b54f99ab1f0b90194387ea&city=${city}`
    );
    return await response.json();
  }

  // Calls API: get forecast for selected city
  async function callApiGetForecast(city) {
    const response = await fetch(
      `https://api.weatherbit.io/v2.0/forecast/daily?key=d61b050238b54f99ab1f0b90194387ea&city=${city}&days=5`
    );
    return await response.json();
  }

  // City selection - triggers search
  async function handleSelect(selection) {
    selectedCity = selection.detail.value;

    // Get current weather data
    const responseCurrentWeather = await callApiGetCurrentWeather(selectedCity);
    console.log(responseCurrentWeather.data[0]);
    weather = responseCurrentWeather.data[0];

    // Get forecast data
    const responseForecast = await callApiGetForecast(selectedCity);
    console.log(responseForecast.data);
    forecast = responseForecast.data;
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
        value:
          city.city_name.toLowerCase() + "," + city.country_code.toLowerCase(),
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
    <img
      src="../icons/{weather.weather.icon}.png"
      alt="current weather icon"
    /><br />
    {weather.weather.description}
    <div id="forecast">
      {#if forecast != undefined} Forecast:<br />
      {#each forecast as day}
      <div class="forecast-day">
        <img
          src="../icons/{day.weather.icon}.png"
          alt="forecast weather icon"
        /><br />
        {day.weather.description}
      </div>
      {/each} {/if}
    </div>
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
    max-width: 500px;
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

  #forecast {
    text-align: left;
  }

  .forecast-day {
    float: left;
    width: 92px;
    margin-right: 8px;
    text-align: center;
  }

  .forecast-day:last-child {
    margin-right: 0px;
  }

  .forecast-day > img {
    width: 60xp;
    height: 60px;
  }
</style>
