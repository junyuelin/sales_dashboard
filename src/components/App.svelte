<script>
  import Graph from '../components/Graph.svelte';
	import { onMount } from 'svelte';
	import * as topojson from 'topojson-client';
	import { geoPath, geoAlbersUsa } from 'd3-geo';
	import { draw } from 'svelte/transition';
	
	const projection = geoAlbersUsa().scale(500).translate([487.5, 305])
	
	const path = geoPath().projection(null);
  
	let todo_category;
	let states = [];
	let mesh;
	let selected;
	//$: console.log({ selected })
	
	onMount(async () => {
		const us = await fetch('https://cdn.jsdelivr.net/npm/us-atlas@3/counties-albers-10m.json')
			.then(d => d.json())
		console.log({ us })
		
		states = topojson.feature(us, us.objects.states).features;
		// using TopoJSON's feature method to convert state into GeoJSON for easy handling and rendering
	
		mesh = topojson.mesh(us, us.objects.states, (a, b) => a !== b);
		
		$: console.log({ states, mesh })
	})
</script>

<svg viewBox="0 0 2000 610"> // viewbox of the US map
	<!-- State shapes -->
	<g fill="white" stroke="black"> // the fill the state color as white and the border as black
		{#each states as feature, i} 
			<path d={path(feature)} on:click={() => selected = feature} class="state" in:draw={{ delay: i * 50, duration: 1000 }} />
		{/each} 
    // each state path has an 'on:click' event to set the selected state, 
    // and uses the 'draw' transition for a delay effect based on its index
				
	</g>
		
	<!-- Interior lines -->
<!-- 	<path d={path(mesh)} fill="none" stroke="black" /> -->

  // if a state is selected, it is highlighted with a semi-transparent gray fill	
	{#if selected}
		<path d={path(selected)} fill="hsl(0 0% 50% / 20%)" stroke="black" stroke-width={2} />
	{/if}
  
</svg>

<div class="selectedName">{selected?.properties.name ?? ''}</div>

<main>

   <section class="graph">
        <h2 style="margin-top: 15px">todo pie</h2>
        <Graph bind:todo_category={todo_category}/>
    </section>

</main>

<style>
	.state:hover {
		fill: hsl(0 0% 50% / 20%);
	}
	
	.selectedName {
		text-align: center;
		margin-top: 8px;
		font-size: 1.5rem;
	}
  .graph {
    display: flex;
    flex-direction: column;
    align-items: center;  /* Centers children horizontally */
    justify-content: center; /* Centers children vertically if needed */
  }

  h2 {
    margin-top: 15px;
    margin-bottom: 20px; /* Adjust space between title and graph */
    text-align: center; /* Center-aligns the text */
  }
</style>
