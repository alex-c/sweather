<script>
  import { onMount } from "svelte";
  import Select from "svelte-select";

  let cities = [];
  let city = "";

  function searchCities(search) {
    return new Promise((resolve, reject) => {
      setTimeout(function() {
        resolve(
          cities.filter(city => city.value.startsWith(search.toLowerCase()))
        );
      }, 100);
    });
  }

  async function callApi(city) {
    const response = await fetch(
      `https://api.weatherbit.io/v2.0/current?key=d61b050238b54f99ab1f0b90194387ea&city=${city}`
    );
    const data = await response.json();
  }

  onMount(async () => {
    const response = await fetch("./cities.json");
    const data = await response.json();
    cities = data.map(city => {
      return { value: city.city_name.toLowerCase(), label: city.city_name };
    });
  });

  const getOptionLabel = option => option.label;
  const getSelectionLabel = option => option.label;

  function handleSelect(selection) {
    city = selection.detail.label;
  }

  function handleClear() {
    city = "";
  }
</script>

<main>
  <h1>Sweather</h1>
  <Select
    loadOptions="{searchCities}"
    placeholder="Enter a city name..."
    {getOptionLabel}
    {getSelectionLabel}
    on:select="{handleSelect}"
    on:clear="{handleClear}"
  ></Select>
  <div>{city}</div>
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
</style>
