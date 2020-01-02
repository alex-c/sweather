<script>
  export let day;
</script>

<div class="forecast-day box">
  {day.valid_date}<br />
  <img
    src="../icons/{day.weather.icon}.png"
    alt="forecast weather icon"
  /><br />
  {day.weather.description}
</div>

<style>
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
</style>
