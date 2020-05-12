<script>
	import { csv, tsv, json } from 'd3-fetch';
	import { scaleLinear, scaleLog } from 'd3-scale';
	import { nest } from 'd3-collection';
	import { format } from 'd3-format';

	import Chart from './Chart.svelte';

	let dataScatterPlot;
	csv('./banque_mondiale.csv')
		.then(d => {
			d.forEach(e => {
				e.gdp = +e.gdp;
				e.life_expectancy = +e.life_expectancy;
				e.population = +e.population;
			});
			dataScatterPlot = d;
		});

	const dataLineChart = Array.from({ length: 100 }, (e, i) => {
		return {
			x: i,
			y: Math.floor(Math.random() * 101)
		};
	});

	let dataBarChart;
	tsv('./top_films_2018.tsv')
		.then(d => {
			dataBarChart = nest()
				.key(d => d.genre)
				.rollup(v => v.length)
				.entries(d);
		});

	let dataBarChartRace;
	json('./barChartRace.json')
		.then(d => {
			dataBarChartRace = nest()
				.key(d => new Date(d.date).getFullYear())
				.rollup(v => v.sort((a, b) => b.value - a.value))
				.entries(d);
			console.log(dataBarChartRace)
		});

	let template = (d) => `
		<h3>${d.country}</h3>
		<p>GDP: <b>${Math.round(d.gdp / 1e6)} million dollars</b></p>
		<p>Life expectancy: <b>${Math.round(d.life_expectancy)} years</b></p>
		<p>Population: <b>${d.population >= 2e6 ? `${Math.round(d.population / 1.6)} million` : d.population}</b></p>
	`

	//let template = (d) => `<h3>${d.key}: ${d.value}</h3>`

	let mobile = window.matchMedia('(max-width: 600px)').matches;
	window.addEventListener('resize', () => {
		mobile = window.matchMedia('(max-width: 600px)').matches;
	});
</script>

<main>
	<h1>Demo - Svelte &times; D3.js</h1>
	<!-- {#if dataBarChartRace}
		<Chart
			type='BarChartRace'
			data={dataBarChartRace}
			title="Highest-valued companies (2000 - 2019)"
			height={400}
			key='key' value='value'
			limit={5}
			animate duration={2000} />
	{/if} -->
	{#if dataBarChart}
		{#if mobile}
			<Chart
				type='BarChart'
				data={dataBarChart}
				title="Genres of top movies of 2018"
				height={600}
				key='key' value='value'
				barOrientation='horizontal' sortBars grid
				animate duration={2000}
				tooltip tooltipTemplate={template} />
		{:else}
			<Chart
				type='BarChart'
				data={dataBarChart}
				title="Genres of top movies of 2018"
				height={400}
				key='key' value='value'
				barOrientation='vertical' sortBars grid
				animate duration={2000}
				tooltip tooltipTemplate="{d => `<p>In 2018, <b>${d.value}</b> of the top movies were <b>${d.key}</b> movies</p>`}" />
		{/if}
	{/if}
	{#if dataLineChart}
		<Chart
			type='LineChart'
			data={dataLineChart}
			title="Some random values"
			width={800} height={500}
			xTicks="{{ value: 5 }}"
			yTicks="{{ value: 14, type: 'every' }}" yGrid
			animate duration={5000} />
	{/if}
	{#if dataScatterPlot}
		<Chart
			type='ScatterPlot'
			data={dataScatterPlot}
			title="Health and wealth of nations"
			height={500}
			x='gdp' xScaleType={scaleLog} xTicks="{{ value: 5 }}" xTickFormat="{ d => format('~s')(d / 1e6) }" xGrid
			y='life_expectancy' yScaleType={scaleLinear} yGrid
			r='population'
			animate duration={2000}
			tooltip tooltipTemplate={template} />
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
