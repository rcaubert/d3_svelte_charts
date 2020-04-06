<script>
    export let xScale;
    export let yScale;
    export let data;
    export let x;
    export let y;
    export let animate;
    export let duration = 1000;

    import { onMount } from 'svelte';
    import { line } from 'd3-shape';
    import { select, selectAll } from 'd3-selection';
    import { transition } from 'd3-transition';

    let g;

    //Les composants

    // <g> est une sorte de groupe de divs
    // <circle> a les parametres cx cy et r
    // <rect> avec param x y width height
    // <line> utilise par les axes
    // <path> : parametre ultra flexible en svg, seul param qui est d qui represente le chemin suivi. Tu lui donnes le point de depart puis des sortes de coordonnees. MO,O M, 10, 10 veut dire tu commences a 00 et tu vas à 10 10 en droite. Mais d3 a plein de fonctions pour generer cett longue chaine d a ta place (voir github d3-shape)

    onMount(() => {
        const lineGenerator = line()
            .x(d => xScale(d[x]))
            .y(d => yScale(d[y]));

        let path = select(g).append('path');

        path.datum(data) //datum pour data au singulier
            .attr('d', lineGenerator)
            .attr('fill', 'none')
            .attr('stroke', 'rebeccapurple')
            .attr('stroke-width', 5) //epaisseur du trait, style css
            .attr('stroke-linecap', 'round')//arrondit les angles début et fin

            if (animate)
            {
                path
                    .attr('stroke-dasharray', path.node().getTotalLength())
                    .attr('stroke-dashoffset', path.node().getTotalLength())
                    .transition()
                    .duration(5000)
                    .attr('stroke-dashoffset', 0)
                    //tu mets dasharray a la longueur de ton chemin et tu fais varier l'offset de la longueur de ton chemin a 0.
            }
    });
</script>


<g bind:this={g}></g>
