<script>
	import data from './data/data.js';
	import { scaleLinear } from 'd3-scale';
	import { max } from 'd3-array';

	import AxisX from './components/AxisX.svelte';
	import AxisY from './components/AxisY.svelte';
	import Tooltip from './components/Tooltip.svelte';

	let width = 400;
	let height = 400;

	const margin = { top: 20, right: 40, left: 10, bottom: 20 };

	$: xScale = scaleLinear()
		.domain([0, 100])
		.range([margin.left, width - margin.left - margin.right]);
	$: yScale = scaleLinear()
		.domain([0, max(data, (d) => d.hours)])
		.range([height - margin.top - margin.bottom, margin.top]);

	let hoveredData;
</script>

<h1>Study longer, score better!</h1>
<!-- svelte-ignore a11y-no-static-element-interactions -->
<div
	class="chart-container"
	bind:clientWidth={width}
	on:mouseleave={() => {
		hoveredData = null;
	}}
>
	<svg
		width={width}
		height={height}
	>
		<AxisX
			height={height}
			xScale={xScale}
			margin={margin}
		/>
		<AxisY
			width={width}
			yScale={yScale}
			margin={margin}
		/>
		<g
			class="circles"
			transform="translate({margin.left}, {margin.top})"
		>
			{#each data.sort((a, b) => a.grade - b.grade) as student}
				<circle
					cx={xScale(student.grade)}
					cy={yScale(student.hours)}
					r={hoveredData && hoveredData == student ? '20' : '10'}
					fill="purple"
					opacity={hoveredData ? (hoveredData == student ? '1' : '0.3') : '1'}
					stroke="black"
					on:mouseover={() => {
						hoveredData = student;
					}}
					on:focus={() => {
						hoveredData = student;
					}}
					tabIndex="0"
				/>
			{/each}
		</g>
	</svg>
	{#if hoveredData}
		<Tooltip
			data={hoveredData}
			xScale={xScale}
			yScale={yScale}
		/>
	{/if}
</div>

<style>
	circle {
		transition: r 300ms ease, opacity 300ms ease;
		cursor: pointer;
	}

	circle:focus {
		outline: none;
	}

	h1 {
		padding: 10px;
		font-size: 2rem;
        font-family: 'Lato', sans-serif;
		font-weight: 500;
		font-style: normal;
	}

	.chart-container {
		font-family: 'Lato', sans-serif;
		font-weight: 400;
		font-style: normal;
	}
</style>
