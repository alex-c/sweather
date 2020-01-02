<script>
  import { onMount } from "svelte";
  import Select from "svelte-select";

  let cities = [];
  let selectedCity = undefined;
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
      `https://api.weatherbit.io/v2.0/forecast/daily?key=d61b050238b54f99ab1f0b90194387ea&city=${city}&days=6`
    );
    return await response.json();
  }

  // City selection - triggers search
  async function handleSelect(selection) {
    selectedCity = selection.detail;

    // Get current weather data
    const responseCurrentWeather = await callApiGetCurrentWeather(
      selectedCity.value
    );
    console.log(responseCurrentWeather.data[0]);
    weather = responseCurrentWeather.data[0];

    // Get forecast data
    const responseForecast = await callApiGetForecast(selectedCity.value);
    console.log(responseForecast.data);
    forecast = responseForecast.data;
  }

  // Round down a number to n decimal positions
  function roundDown(number, decimals) {
    decimals = decimals || 0;
    return Math.floor(number * Math.pow(10, decimals)) / Math.pow(10, decimals);
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
    ></Select>
  </div>
  {#if weather != undefined}
  <div id="weather-container">
    <div id="weather">
      <img
        src="../icons/{weather.weather.icon}.png"
        alt="current weather icon"
      />
      <p>
        <strong>{selectedCity.label}</strong><br />
        <span>Weather: {weather.weather.description}</span><br />
        <span>Temperature: {weather.temp}°C</span><br />
        <span
          >Wind: {roundDown(weather.wind_spd)} m/s, {weather.wind_cdir}</span
        >
      </p>
    </div>
    <div id="forecast">
      {#if forecast != undefined} {#each forecast as day}
      <div class="forecast-day">
        {day.valid_date}<br />
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
<a
  id="attribution"
  href="https://unsplash.com/photos/q8JdZX1jNDc"
  target="_blank"
>
  Background image by Ahmad Bilal on Unplash
</a>

<style>
  :global(body) {
    margin-top: 16px;
    text-align: center;
    background-image: url("../bg.jpg");
  }

  main {
    margin: auto;
    padding: 1em;
    padding-top: 0em;
    text-align: center;
    max-width: 500px;
    background-color: rgba(255, 255, 255, 0.8);
    border-radius: 3px;
    overflow: auto;
  }

  h1 {
    color: #ff003e;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
    text-shadow: 1px 1px 1px black;
  }

  #city-search-box {
    text-align: left;
    margin-bottom: 20px;
  }

  #weather-container {
    text-align: left;
  }

  #weather {
    overflow: auto;
    background-color: rgba(255, 255, 255, 0.5);
    border-radius: 3px;
  }

  #weather > img {
    float: left;
  }

  #forecast {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }

  .forecast-day {
    width: 160px;
    margin-top: 8px;
    background-color: rgba(255, 255, 255, 0.5);
    text-align: center;
  }

  .forecast-day > img {
    width: 60xp;
    height: 60px;
  }

  #attribution {
    color: white;
    text-shadow: 1px 1px 1px black;
  }
</style>
