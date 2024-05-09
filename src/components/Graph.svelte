<script>
	import * as d3 from 'd3';
    export let selectedStateData = [];
    console.log('Graph loaded');

    let hovered = -1; 
    
	let arcGenerator = d3.arc()
		.innerRadius(10)
		.outerRadius(100)
		.padAngle(.02)
		.cornerRadius(4);

	let pieAngleGenerator = d3.pie().value(d => d.operating_profit);

	const arc_color = d3.scaleLinear()
		.range(["#faafd1", "#db921d", "#b86a04", "#a65d29" ,"#6e3003", "#9e3003"]);
    $: arc_data = pieAngleGenerator(selectedStateData);

</script>

<div class="visualization">
	<svg width="540" height="1200">
		<g transform="translate(100, 900)">
			<!-- Place for Pie -->
			{#each arc_data as data, index}
			<path 
				d={arcGenerator({
					startAngle: data.startAngle,
					endAngle: data.endAngle
				})}
				fill={index === hovered ? "brown": arc_color(index)}
				on:mouseover={(event) => { hovered = index; }}
				on:mouseout={(event) => { hovered = -1; }}
			/>
			{/each}
		</g>
	</svg>
</div>
<style>
	.visualization {
		font: 50px sans-serif;
		margin: auto;
		margin-top: 1px;
		text-align: middle;
	}
</style>  
