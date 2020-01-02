<script>
  import ForecastDay from "./ForecastDay.svelte";

  export let forecast;
</script>

<div id="forecast">
  {#each forecast as day}
  <ForecastDay {day} />
  {/each}
</div>

<style>
  #forecast {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }
</style>
