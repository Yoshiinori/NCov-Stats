<div class='container-fluid stats'>
	<canvas id="chart"></canvas>
</div>

<script>
  import {afterUpdate, onMount} from 'svelte';

  let chart;

  export let covidData = {
		deaths: 0,
		recovered: 0,
		confirmed: 0,
		country: 'Loading'
  }
  
  const newChart = () => {
    if (chart) chart.destroy()
    const {confirmed, deaths, recovered, country} = covidData;
    const ctx = document.getElementById('chart').getContext('2d');  
    chart = new Chart(ctx, {
    type: 'bar',
    data: {
        labels: ['Deaths', 'Confirmed', 'Recovered'],
        datasets: [{
            label: `${country} COVID Statistics`,
            data: [deaths, confirmed, recovered],
            backgroundColor: [
                'red',
                'blue',
                'green'
            ],
        }]
    },
    options: {
      maintainAspectRatio: false
    }
  });
  };

  afterUpdate(newChart)
</script>

<style>
  .stats {
		height: 70%;
	}
</style>

