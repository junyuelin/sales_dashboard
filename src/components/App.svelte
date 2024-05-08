<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let svg;

  onMount(async () => {
    const width = 960, height = 600;
    
    const projection = d3.geoAlbersUsa()
        .translate([width / 2, height / 2])
        .scale(1300);

    const path = d3.geoPath().projection(projection);

    const data = await d3.json('static/us-states.json');

    d3.select(svg)
      .selectAll('path')
      .data(data.features)
      .enter()
      .append('path')
      .attr('d', path)
      .attr('fill', '#ccc')
      .attr('stroke', 'white')
      .attr('stroke-width', 1.5);
  });
</script>

<style>
  svg {
    width: 100%;
    height: auto;
    max-width: 960px;
    margin: auto;
    display: block;
    background-color: #f4f4f4;
  }
</style>

<svg bind:this={svg}></svg>
