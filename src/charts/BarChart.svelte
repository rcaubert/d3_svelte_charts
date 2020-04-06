<script>
    export let scale;
    export let labels;
    export let data;
    export let key;
    export let value;
    export let animate;
    export let duration;
    export let barOrientation;
    export let sortBars;

    import { onMount } from 'svelte';
    import { select, selectAll } from 'd3-selection';
    import { scaleOrdinal } from 'd3-scale';
    //scale sqrt pour square root : quand tu doubles la valeur tu multiplies par la racine carree au lieu de mult par 4
    import { transition } from 'd3-transition';

    let g;

    onMount(() => {
        if (sortBars)
            data = data.sort((a, b) => b[value] - a[value]);
        let bars = select(g).selectAll('rect') //rect pour rectangle
            .data(data).enter()
            .append('rect') //4 parametres : xy (coordonnees en haut a gauche), width, height
                .attr('x', d => barOrientation === 'vertical' ? labels(d[key]) : scale(0))
                .attr('y', d => barOrientation === 'vertical' ? scale(d[value]) : labels(d[key]))
                .attr('width', barOrientation === 'vertical' ? labels.bandwidth() : d => scale(d[value]) - scale(0))
                .attr('height', barOrientation === 'vertical' ? d => scale(0) - scale(d[value]) : labels.bandwidth()) //distance entre bas de l'axe et le haut de ta barre. Contre intuitif, car c'est inversé
                .attr('fill', 'rebeccapurple');

    //    if (animate) {
    //        circle = circle
    //            .attr('r', 0) // tu lui donnes la taille de 0 puis il grandit jusqua //la taille souhaitee
    //            .transition()
    //            .duration(duration);
    //    }
    //    circle.attr('r', typeof r === 'string'
    //        ? d => radiusScale(d[r])
    //        : r
    //    );
    });
</script>


<g bind:this={g}></g>
