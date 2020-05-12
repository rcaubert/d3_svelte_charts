<script>
	import { beforeUpdate } from 'svelte';
	import { select, selectAll } from 'd3-selection';
	import { scaleSqrt } from 'd3-scale';
	import { extent } from 'd3-array';
	import { transition } from 'd3-transition';

	export let data;
	export let x;
	export let xScale;
	export let y;
	export let yScale;
	export let r;
	export let animate;
	export let duration;
	export let tooltipRef;
	export let tooltipTemplate;

	let g;

	beforeUpdate(() => {
		if (!g || !xScale || !yScale) return;

		select(g).selectAll('*').remove();

		if (typeof r === 'string') {
			var radiusScale = scaleSqrt()
				.domain(extent(data, d => d[r]))
				.range([2, 50]);
		}

		let circles = select(g).selectAll('circle')
			.data(data).enter()
			.append('circle')
				.attr('cx', d => xScale(d[x]))
				.attr('cy', d => yScale(d[y]))
				.attr('fill', 'rebeccapurple');

		if (tooltipRef) {
			select(tooltipRef)
				.style('transform', 'translate(-50%, -120%)');

			circles = circles
				.style('cursor', 'pointer')
				.on('mouseenter', d => {
					select(tooltipRef)
						.html(tooltipTemplate(d))
						.style('left', `${xScale(d[x])}px`)
						.style('top', `${yScale(d[y])}px`)
						.style('opacity', 1);
				})
				.on('mouseleave', d => {
					select(tooltipRef).style('opacity', 0)
				});
		}

		if (animate) {
			let onScroll;
			window.addEventListener('scroll', onScroll = () => {
				//console.log(window.scrollY, g.parentNode.getBoundingClientRect())
				if (window.scrollY > g.parentNode.getBoundingClientRect().top) {
					circles
						.attr('r', 0)
						.transition()
						.duration(duration)
							.attr('r', typeof r === 'string'
								? d => radiusScale(d[r])
								: r
							);

					window.removeEventListener('scroll', onScroll);
				}
		});
	}
	else {
		circles.attr('r', typeof r === 'string'
			? d => radiusScale(d[r])
			: r
		);
	}

	});
</script>

<g bind:this={g} class="scatter-plot"></g>
