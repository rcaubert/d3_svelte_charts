<script>
	import { onMount } from 'svelte';
	import { extent } from 'd3-array';

	import Axis from '../components/Axis.svelte';
	import Grid from '../components/Grid.svelte';
	import ScatterPlot from './ScatterPlot.svelte';
	import LineChart from './LineChart.svelte';

	// General props
	export let type;
	export let data;

	// Layout props
	export let height;
	export let margin;

	export let x;
	export let xScaleType;
	export let xAxisOrient;
	export let xTicks;
	export let xTickFormat;
	export let xGrid;
	export let y;
	export let yScaleType;
	export let yAxisOrient;
	export let yTicks;
	export let yTickFormat;
	export let yGrid;
	export let r; // For ScatterPlot

	// Props for animation
	export let animate;
	export let duration;

	//Props for tooltips
	export let tooltipRef;
	export let tooltipTemplate;

	let svg;
	let width;

	let xScale;

	let yScale;

	const getWidth = () => {
		return svg.parentNode.getBoundingClientRect().width;
	}

	const setScaleRanges = () => {
		xScale = xScale.range([margin, width - margin]);
		yScale = yScale.range([height - margin, margin]);
	}

	//responsivity
	onMount(() => {
	let timeout;
	window.addEventListener('resize', () => {
		clearTimeout(timeout);
		timeout = setTimeout(() => {
			width = getWidth();
			setScaleRanges();
		}, 50);
	});

	width = getWidth();
	xScale = xScaleType()
		.domain(extent(data, d => d[x]));
	yScale = yScaleType()
		.domain(extent(data, d => d[y]));
	setScaleRanges();
})

</script>

<svg bind:this={svg} {width} {height} class="chart">
	{#if type === 'ScatterPlot'}
		{#if xGrid}
			<Grid {width} {height} {margin} scale={xScale} direction='vertical' ticks={xTicks} />
		{/if}
		{#if yGrid}
			<Grid {width} {height} {margin} scale={yScale} direction='horizontal' ticks={yTicks} />
		{/if}
		<Axis {width} {height} {margin} scale={xScale} orient={xAxisOrient} ticks={xTicks} tickFormat={xTickFormat} />
		<Axis {width} {height} {margin} scale={yScale} orient={yAxisOrient} ticks={yTicks} tickFormat={yTickFormat} />
		<ScatterPlot
			{data}
			{x} {xScale}
			{y} {yScale}
			{r}
			{animate} {duration}
			{tooltipRef} {tooltipTemplate} />
	{:else if type === 'LineChart'}
		{#if xGrid}
			<Grid {width} {height} {margin} scale={xScale} direction='vertical' ticks={xTicks} />
		{/if}
		{#if yGrid}
			<Grid {width} {height} {margin} scale={yScale} direction='horizontal' ticks={yTicks} />
		{/if}
		<Axis {width} {height} {margin} scale={xScale} orient={xAxisOrient} ticks={xTicks} tickFormat={xTickFormat} />
		<Axis {width} {height} {margin} scale={yScale} orient={yAxisOrient} ticks={yTicks} tickFormat={yTickFormat} />
		<LineChart
			{data}
			{x} {xScale}
			{y} {yScale}
			{animate} {duration} />
	{/if}
</svg>
