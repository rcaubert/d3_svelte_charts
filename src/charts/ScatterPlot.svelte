<script>
    export let xScale;
    export let yScale;
    export let data;
    export let x;
    export let y;
    export let r = 10;
    export let animate;
    export let duration;
    //par defaut duration a 1 seconde

    import { onMount } from 'svelte';
    import { select, selectAll } from 'd3-selection';
    import { scaleSqrt } from 'd3-scale';
    //scale sqrt pour square root : quand tu doubles la valeur tu multiplies par la racine carree au lieu de mult par 4
    import { extent } from 'd3-array';
    import { transition } from 'd3-transition';

    let g;

    onMount(() => {
        if (typeof r === 'string') {
            var radiusScale = scaleSqrt()
                .domain(extent(data, d => d[r]))
                .range([2, 50]);
                //quelle taille va etre le plus petit et le plus grand rond. dependra de la taille de notre graphique
        }
        //tu selectionnes d'abord les cercles comme ça il sait la diff entre ceux qu'il doit envoyer, modifier, supprimer
        let circle = select(g).selectAll('circle')
            .data(data).enter()
            .append('circle')
                .attr('cx', d => xScale(d[x]))
                //tu peux remplacer gdp par x pour passer en props c qui correspond au x
                .attr('cy', d => yScale(d[y]))
                .attr('fill', 'rebeccapurple');

        if (animate) {
            circle = circle
                .attr('r', 0) // tu lui donnes la taille de 0 puis il grandit jusqua la taille souhaitee
                .transition()
                .duration(duration);
        }
        circle.attr('r', typeof r === 'string'
            ? d => radiusScale(d[r])
            : r
        );
    });
</script>


<g bind:this={g}></g>
