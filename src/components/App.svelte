<script>
	import Graph from '../components/Graph.svelte';
	import { onMount } from 'svelte';
	import * as topojson from 'topojson-client';
	import { geoPath, geoAlbersUsa } from 'd3-geo';
	import { draw } from 'svelte/transition';
	import * as d3 from 'd3';
	
	
	let allData = [];
	let selectedStateData = [];
	let states = [];
	let selected;
	let mesh;
	let tooltip = { visible: false, x: 0, y: 0, stateName: '', totalProfit: 0, topProduct: '' }; // 工具提示

	const projection = geoAlbersUsa().scale(500).translate([487.5, 305])
	const path = geoPath().projection(null);
	
	let colorScale = () => 'lightgray';
	let maxProfit = 0;
	
	onMount(async () => {
		const us = await fetch('https://cdn.jsdelivr.net/npm/us-atlas@3/counties-albers-10m.json')
			.then(d => d.json())
		console.log({ us })
		
		states = topojson.feature(us, us.objects.states).features;
		// using TopoJSON's feature method to convert state into GeoJSON for easy handling and rendering
	
		mesh = topojson.mesh(us, us.objects.states, (a, b) => a !== b);
		//$: console.log({ states, mesh })
		
		allData = await d3.csv('https://raw.githubusercontent.com/junyuelin/sales_dashboard/main/static/cleaned_2021.csv');
		console.log(allData);

		// 汇总州级别的销售数据
		aggregateDataPerState();
		setupColorScale();
	})

	// 汇总每个州的数据
	function aggregateDataPerState() {
		const stateProfitData = d3.group(allData, d => d.State);
		states = states.map(state => {
			const stateData = stateProfitData.get(state.properties.name) || [];
			const totalProfit = d3.sum(stateData, d => d.operating_profit);
			const topProductEntry = stateData.reduce((max, d) => (d.operating_profit > (max?.operating_profit || 0) ? d : max), null);
			const topProduct = topProductEntry ? topProductEntry.Product : 'N/A';
			return { ...state, totalProfit, topProduct };
		});
	}

	// 设置颜色比例尺
	function setupColorScale() {
		maxProfit = d3.max(states, d => d.totalProfit);
		console.log("Max Profit:", maxProfit); // Debugging line
		colorScale = d3.scaleSequential(d3.interpolateBlues).domain([0, maxProfit]);
	}
	
	// 显示工具提示
	function showTooltip(event, state) {
		tooltip = {
			visible: true,
			x: event.pageX,
			y: event.pageY,
			stateName: state.properties.name,
			totalProfit: state.totalProfit,
			topProduct: state.topProduct
		};
	}
	
	// 隐藏工具提示
	function hideTooltip() {
		tooltip.visible = false;
	}
	
	
	function handleStateClick(feature) {
		selected = feature;
		const stateName = feature.properties.name;
		console.log(stateName);
		
		selectedStateData = allData.filter(d => d.State == stateName);
		selectedStateData = d3.rollups(
			selectedStateData, 
			v => d3.sum(v, d => d.operating_profit), 
			d => d.Product
		).map(([Product, operating_profit]) => ({Product, operating_profit}));
		console.log("Selected State Data: ", selectedStateData);
	}
</script>

<div class = "container" style="height: 500px;">
	{#if tooltip.visible}
	<div class="tooltip" style="top: {tooltip.y}px; left: {tooltip.x}px;">
		<strong>{tooltip.stateName}</strong><br>
		Total Sales Profit: {tooltip.totalProfit}<br>
		Top Product: {tooltip.topProduct}
	</div>
	{/if}
	<svg viewBox="-400 -100 1560 1000">
		<!-- 州形状，大小和位置 -->
		<g fill="white" stroke="black">
			{#each states as feature, i}
			<path
			d={path(feature)}
			fill={feature.totalProfit > 0 ? colorScale(feature.totalProfit) : 'lightgray'}
			on:mouseenter={event => showTooltip(event, feature)}
			on:mouseleave={hideTooltip}
			on:click={() => handleStateClick(feature)}
			class="state"
			in:draw={{ delay: i * 50, duration: 1000 }}
			/>
			{/each}
		</g>
		
		<!-- 内部线条 -->
		<path d={path(mesh)} fill="none" stroke="black" />
		
		<!-- 如果选择了某个州，则以pink色填充显示 -->
		{#if selected}
		<path
			d={path(selected)}
			fill=pink
			stroke="black"
			stroke-width={2}
		/>
		{/if}
	</svg>
	<div class="legend">
		<h2>Total Sales Profit</h2>
		<svg width="300" height="65">
		  <defs>
			<linearGradient id="color-gradient">
			  {#each d3.range(0, 1.01, 0.1) as t}
				<stop offset="{t * 100}%" stop-color="{colorScale(t * maxProfit)}" />
			  {/each}
			</linearGradient>
		  </defs>
		  <rect width="300" height="15" fill="url(#color-gradient)" />
		  <g transform="translate(0, 25)">
			<text x="0" y="10" font-size="10">0</text>
			<text x="260" y="10" font-size="10">{maxProfit.toLocaleString()}</text>
		  </g>
		</svg>
	</div>
</div>

<main>
   <section class="graph">
		<h1 class="title"> Addidas Sales Dashboard</h1>
		<div class="selectedName">
            {#if selected}
                {selectedStateData.length > 0 ? `Operating Profit by Product in ${selected.properties.name}` : 'Oops! Data is not available.'}
            {:else}
                Click on the map to interact!
            {/if}
        </div>
        <Graph {selectedStateData} />
    </section>
</main>

<style>
    .legend h2 {
        font-size: 1rem; /* Adjust font size */
        color: #333; /* Adjust text color */
        margin-bottom: 10px; /* Add margin for spacing */
    }

	.tooltip {
		position: absolute;
		pointer-events: none;
		background: white;
		border: 1px solid #ddd;
		padding: 5px;
		border-radius: 3px;
	}

	.state:hover {
		fill: pink;
	}
	
	.selectedName {
		position: absolute;
		top: 145px;
   		left: 962px;
		margin-top: 30px;
		font-family: Arial, sans-serif;
		font-size: 20px;
	}
	
	.graph {
		display: flex;
		flex-direction: column;
		align-items: flex-end; /* Align title to the left */
		justify-content: center; 
		padding-left: 57%; /* Shift graph section to the left */
		margin-top: -600px; /* Adjust this value to align vertically */
	}
	
	.container {
		width: 75%; /* Adjust the width as needed */
		position: relative;
		left: -10%; /* Moves the container to the left */
		margin-top: 90px; /* Moves the map down */
	}
	
	.legend {
		position: absolute;
		top: 15px;
   		left: 550px;
    	margin-top: 20px;
		margin-left: 60px;
	}

	h1 {
        position: absolute;
        top: 10px; /* Adjust as needed */
        left: 50%; /* Center the title horizontally */
        transform: translateX(-50%); /* Center the title horizontally */
        font-size: 2rem; /* Adjust the font size as needed */
        color: #333; /* Adjust the color as needed */
    }
</style>