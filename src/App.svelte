<script>
	import Chart from './Chart.svelte';

	let countryInput = '';

	let covidData = {
		deaths: 0,
		recovered: 0,
		confirmed: 0,
		country: 'Loading'
	}

	

	const getCountries = async () => {
    const res = await fetch('https://corona-api.com/countries');
    const data = await res.json();

    if (res.ok) {
      return data;
    } else {
      throw new Error(data);
    }
	};


	
	const getStats = async () => {
		const countries = await getCountries();
		if (countryInput !== 'Global') {
			const findCountry = countries.data.find(country => country.name === countryInput);
			covidData = {
				deaths: findCountry.latest_data.deaths,
				recovered: findCountry.latest_data.recovered,
				confirmed: findCountry.latest_data.confirmed,
				country: findCountry.name
			};
		} else if (countryInput === 'Global') {
			countryInput = 'Global'
			fetch('https://corona-api.com/timeline')
			.then(res => res.json())
			.then(data => {
				const {confirmed, deaths, recovered} = data.data[1];
				covidData = {
					deaths: deaths,
					recovered: recovered,
					confirmed: confirmed,
					country: 'Global'
				};
			});
		};
	};


	countryInput = 'Global'
	fetch('https://corona-api.com/timeline')
	.then(res => res.json())
	.then(data => {
		const {confirmed, deaths, recovered} = data.data[1];
		covidData = {
			deaths: deaths,
			recovered: recovered,
			confirmed: confirmed,
			country: 'Global'
		};
	});

</script>

<style>
	:global(body) {
		background-color: rgb(21, 20, 22);
	}
</style>

<!-- Header -->

<div class="container-fluid d-flex flex-column justify-content-center align-items-center">
	
		<h1 class="text-center text-white m-3">NCov Stats</h1>

		<select bind:value={countryInput} class="bg-dark text-white form-select w-75" aria-label="Select a country">
		{#await getCountries()}
		<option selected>Loading</option>
		{:then countries}
			<option selected>Global</option>
			{#each countries.data as country}
				<option value="{country.name}">{country.name}</option>
			{/each}
		{:catch error}
			<p style="color: red">{error.message}</p>
		{/await}
		</select> 
		<button on:click={getStats} class="btn btn-outline-light">Search!</button>
		
</div>

<!-- Statistics -->
<Chart covidData={covidData}/>

