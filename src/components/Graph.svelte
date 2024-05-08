<script>
	import * as d3 from 'd3';
    export let todo_category = [];
    console.log('Graph loaded');
	let arcGenerator = d3.arc()
		.innerRadius(10)
		.outerRadius(100)
		.padAngle(.02)
		.cornerRadius(4);
	let pieAngleGenerator = d3.pie();
	let sample_data = [1,4,5,2,4]
	let arc_data = pieAngleGenerator(sample_data);
	console.log(JSON.stringify(arc_data));

</script>

<div class="visualization">
	<svg width="500" height="500">
		<g transform="translate(250, 120)">
			<!-- Place for Pie -->
			{#each arc_data as data, index}
			<path 
				d={arcGenerator({
					startAngle: data.startAngle,
					endAngle: data.endAngle
				})}
			/>
			{/each}
		</g>
	</svg>
</div>
<style>
	.visualization {
		font: 25px sans-serif;
		margin: auto;
		margin-top: 1px;
		text-align: middle;
	}
</style>  
