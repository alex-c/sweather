<script>
  export let selectedCity;
  export let weather;

  // Round down a number to n decimal positions
  function roundDown(number, decimals) {
    decimals = decimals || 0;
    return Math.floor(number * Math.pow(10, decimals)) / Math.pow(10, decimals);
  }
</script>

<div id="weather" class="box">
  <img src="../icons/{weather.weather.icon}.png" alt="current weather icon" />
  <p>
    <strong>{selectedCity.label}</strong><br />
    <span>Weather: {weather.weather.description}</span><br />
    <span>Temperature: {weather.temp}°C</span><br />
    <span>Wind: {roundDown(weather.wind_spd)} m/s, {weather.wind_cdir}</span>
  </p>
</div>

<style>
  #weather {
    overflow: auto;
    background-color: rgba(255, 255, 255, 0.5);
  }

  #weather > img {
    float: left;
  }
</style>
