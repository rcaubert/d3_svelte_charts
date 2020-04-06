<script>
	import { csv, tsv } from 'd3-fetch';
	import { scaleLinear, scaleLog } from 'd3-scale';
	import { nest } from 'd3-collection';

	import Chart from './Chart.svelte';

	let data;

	csv('./banque_mondiale.csv')
		.then(d => {
			d.forEach(e => {
				e.gdp = +e.gdp;
				e.life_expectancy = +e.life_expectancy;
				e.population = +e.population;
				//on met toutes les data en nombres
			});
		data = d;
		//on initialise le data qu on va envoyer a notre fichier
	});
//maintenant on cree un datalinechart. Tableau de taille 100 qu'on remplit avec map. On genere un nb aleatoire entre 0 t 100
	const dataLineChart = Array.from({ length:100 }, (e, i) => {
		return {
			x: i,
			y: Math.floor(Math.random() * 101)
		};
});

	let dataBarChart;
	tsv('./top_films_2018.tsv')
		.then(d => {
			dataBarChart = nest()
			//on veut regrouper les films par genre
				.key(d => d.genre)
				.rollup(v => v.length)
				.entries(d);
			console.log(data)
		});
	console.log(dataLineChart)
</script>

<main>
	{#if dataBarChart}
		<Chart
			type = 'BarChart'
			title="Genres of top 2018 movies"
			width={800}
			height={500}
			data={dataBarChart}
			barOrientation='horizontal'
			sortBars
			key='key'
			value='value'
			animate duration={2000} />
			<!-- key label de la barre-->
			<!--value hauteur de la barre-->
	{/if}
	{#if dataLineChart}
		<Chart
		type = 'LineChart'
		title="Some random values"
		width={800}
		height={500}
		data={dataLineChart}
		x='x'
		y='y'
		animate duration={5000} />
	{/if}

	{#if data}
		<Chart
			type = 'ScatterPlot'
			title="Mon super graphique"
			width={800}
			height={500}
			{data}
			x='gdp'
			xScaleType = {scaleLog}
			y='life_expectancy'
			yScaleType = {scaleLinear}
			r='population'
			animate duration={2000} />
		<!--la fonction eval permet d'effectuer du javascript dans du texte. Donc normalement xscale permet d'utiliser scale log dans chart. Nombres entre accolades dans svelte-->
	{/if}
</main>

<style lang='scss'>
	@import './global.scss';

	main {
		text-align: center;
		padding: 1em;
		width: 75%;
		margin: 0 auto;


		@include md {
			width: 100%;
		}
	}
</style>
