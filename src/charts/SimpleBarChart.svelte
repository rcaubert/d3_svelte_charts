<script>
	import { beforeUpdate } from 'svelte';
	import { select, selectAll } from 'd3-selection';
	import { scaleBand } from 'd3-scale';
	import { transition } from 'd3-transition';

	export let data;
	export let key;
	export let labels;
	export let value;
	export let scale;
	export let barOrientation;
	export let animate;
	export let duration;
	export let tooltipRef;
	export let tooltipTemplate;

	let g;

	beforeUpdate(() => {
		if (!g || !labels || !scale) return;
		select(g).selectAll('*').remove();

		let bars = select(g).selectAll('rect')
			.data(data).enter()
			.append('rect')
				.attr('x', d => barOrientation === 'vertical' ? labels(d[key]) : scale(0))
				.attr('fill', 'rebeccapurple');

		if (tooltipRef) {
			select(tooltipRef)
				.style('transform', barOrientation === 'vertical' ? 'translate(-50%, -120%)' :
				 'translate(20%, -50%)');

			bars = bars
				.style('cursor', 'pointer')
				.on('mouseenter', d => {
					select(tooltipRef)
						.html(tooltipTemplate(d))
						.style('left', barOrientation === 'vertical' ? 	`${labels(d[key])+ labels.bandwidth() /2}px` :
						`${scale(d[value])}px`
						)
						.style('top', barOrientation === 'vertical' ? `${scale(d[value])}px` :
						`${labels(d[key]) + labels.bandwidth() /2}px`
						)
						.style('opacity', 1);
				})
				.on('mouseleave', d => {
					select(tooltipRef).style('opacity', 0)
				});
		}

		if (animate) {
			let onScroll;
			window.addEventListener('scroll', onScroll = () => {
				if (window.scrollY > g.parentNode.getBoundingClientRect().top) {
					bars
						.attr('y', d => barOrientation === 'vertical' ? scale(0) : labels(d[key]))
						.attr('width', barOrientation === 'vertical' ? labels.bandwidth() : 0)
						.attr('height', barOrientation === 'vertical' ? 0 : labels.bandwidth())
						.transition()
						.duration(duration)
							.attr('y', d => barOrientation === 'vertical' ? scale(d[value]) : labels(d[key]))
							.attr('height', barOrientation === 'vertical' ? d => scale(0) - scale(d[value]) : labels.bandwidth())
							.attr('width', barOrientation === 'vertical' ? labels.bandwidth() : d => scale(d[value]) - scale(0));

					window.removeEventListener('scroll', onScroll);
				}
			});
		}
		else {
			bars
				.attr('y', d => barOrientation === 'vertical' ? scale(d[value]) : labels(d[key]))
				.attr('height', barOrientation === 'vertical' ? d => scale(0) - scale(d[value]) : labels.bandwidth())
				.attr('width', barOrientation === 'vertical' ? labels.bandwidth() : d => scale(d[value]) - scale(0));
			}
	});
</script>

<g bind:this={g} class="bar-chart"></g>
