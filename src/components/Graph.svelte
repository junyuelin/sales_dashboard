<script>
    import * as d3 from 'd3';
    export let selectedStateData = [];
    console.log('Graph loaded');
    let hovered = -1; 
    
    let arcGenerator = d3.arc()
        .innerRadius(10)
        .outerRadius(200)
        .padAngle(.02)
        .cornerRadius(4);
    let pieAngleGenerator = d3.pie().value(d => d.operating_profit);
    const arc_color = d3.scaleOrdinal()
    .range(["#a69cac","#98c1d9", "#e0fbfc", "#ee6c4d","#fedc97", "#7dcfb6"]);

   
    // define function to format operating profit 
    $: arc_data = pieAngleGenerator(selectedStateData);
    // generate pie chart
</script>
<div class="visualization">
    <svg width="3000" height="2400">
    <!-- container size-->
        <g transform="translate(250, 1300)">
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
        
        <!-- Text for product name and operating profit --> 
        <text 
            transform={`translate(${arcGenerator.centroid(data)})`} 
            dy=".35em" 
            text-anchor="middle"
            fill="black"
            font-size="12"
        >
            {data.data.Product}: {(data.data.operating_profit / d3.sum(selectedStateData, d => d.operating_profit) * 100).toFixed(2)}%
		<!-- calcualte percentage here-->
        </text>
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