<script>
    export let width;
    export let height;
    export let margin = 50;
    export let scale;
    export let orient = 'bottom';

    import { onMount } from 'svelte';
        //execute apres que l'element a ete cree
    import { select} from 'd3-selection';
    import {axisBottom, axisTop, axisLeft, axisRight } from 'd3-axis';
    let g;//il faut trouver une manière de lier notre variable g à notre elem g. donc on le fait en dessous : bind. Donc on lui dit que g c'est g

    onMount(() => {
        switch(orient) {
            case 'bottom':
                select(g)
                    .attr('transform', `translate(0, ${height - margin})`)
                    .call(axisBottom(scale));
                break;
            case 'top':
                select(g)
                    .attr('transform', `translate(0, ${margin})`)
                    .call(axisTop(scale));
                break;
            case 'left':
                select(g)
                    .attr('transform', `translate(${margin}, 0)`)
                    .call(axisLeft(scale));
                break;
            case 'right':
                select(g)
                    .attr('transform', `translate(${width - margin}, 0)`)
                    .call(axisRight(scale));
        }
    });
</script>

<g bind:this={g}></g>
