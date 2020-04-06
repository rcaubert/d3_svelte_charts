<script>
    import { extent, max } from 'd3-array';
    import { scaleLinear, scaleLog, scaleBand } from 'd3-scale';

    export let type;
    export let width;
    export let height;
    export let margin = 50;
    //valeur par defaut
    export let title;
    export let data;
    export let x;
    export let xScaleType = scaleLinear;
    export let y;
    export let yScaleType = scaleLinear;
    export let r = 10;
    //ne sera valable que pour le scatterPlot
    export let key;
    export let value;
    export let barOrientation = "vertical";
    export let sortBars = false;
    export let animate = false;
    export let duration = 1000;

    import Axis from './components/Axis.svelte';
    import ScatterPlot from './charts/ScatterPlot.svelte';
    import LineChart from './charts/LineChart.svelte';
    import BarChart from './charts/BarChart.svelte';

    console.log(data);

    const xScale = xScaleType()
        .domain(extent(data, d => d[x]))
        //tu lui dis d3.extent te genere un tableau où la premiere case est minimum des gdp et la derniere c'est le max. Tu peux aussi utiliser d3.min ou d3.max pour des diagrammes en barres
        .range([margin, width - margin]);

    const yScale = yScaleType()
        .domain(extent(data, d => d[y]))
        .range([height - margin, margin]);

    if (sortBars)
        data = data.sort((a, b) => b[value] - a[value]);

    const scale = scaleLinear() //on cree notre echelle juste pour les Barcharts<
        .domain([0, max(data, d => d[value])])
        .range(barOrientation === 'vertical'
        ? [height - margin, margin]
        : [margin, width - margin]);

    const labels = scaleBand()
        .domain(data.map(e => e[key])) //nous donne un tableau avec juste les keys
        .range(barOrientation === 'vertical'
        ? [margin, width - margin]
        : [margin, height - margin])
        .paddingInner(0.1)
</script>

<h2>{title}</h2>
<svg {width} {height}>
    {#if type === 'ScatterPlot'}
        <ScatterPlot
            {xScale} {yScale} {data}
            {x} {y} {r} {animate} {duration} />
        <!-- notre r peut etre soit la valeur soit la clé de ce quon veut considerer pour le rayon
        a la place de population tu peux mettre r=[10] par exemple-->
        <Axis {width} {height} {margin} scale={xScale} orient='bottom' />
        <Axis {width} {height} {margin} scale={yScale} orient='left' />
    {:else if type === 'LineChart'}
        <LineChart
            {xScale} {yScale} {data}
            {x} {y} {animate} {duration} />
        <Axis {width} {height} {margin} scale={xScale} orient='bottom' />
        <Axis {width} {height} {margin} scale={yScale} orient='left' />
    {:else if type === 'BarChart'}
        <BarChart
            {labels} {scale} {data} {barOrientation}
            {key} {value} {animate} {duration} />
        <Axis {width} {height} {margin} scale={labels} orient="{barOrientation === 'vertical' ? 'bottom' : 'left'}" />
        <Axis {width} {height} {margin} scale={scale} orient="{barOrientation === 'vertical' ? 'left' : 'bottom'}" />
    {/if}
</svg>
