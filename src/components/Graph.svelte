
<script>
    import * as d3 from 'd3';
    export let selectedStateData = [];
    console.log('Graph loaded');
    let hovered = -1; 
    
    let arcGenerator = d3.arc()
        .innerRadius(250*0.5)
        .outerRadius(250*0.8)
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
            {(data.data.operating_profit / d3.sum(selectedStateData, d => d.operating_profit) * 100).toFixed(2)}%
        <!-- calcualte percentage here-->
        </text>
            {/each}
        </g>
    </svg>
</div>

<!-- Legend -->
<div class="legend">
    {#each arc_data as data, index}
    <div class="legend-item">
        <span 
            class="legend-color"
            style="background-color: {arc_color(index)};"
        ></span>
        <span class="legend-circle" style="background-color: {arc_color(index)};"></span> <!-- Dynamically set background color -->
        <span class="legend-text">{data.data.Product}</span>
    </div>
    {/each}
</div>


<style>
    .visualization {
        font: 50px sans-serif;
        margin: auto;
        margin-top: 1px;
        text-align: middle;
    }

    .legend {
    position: absolute;
    top: 1300px; /* Adjust the top offset */
    left: 1600px; /* Adjust the left offset */
}
    .legend-item {
        margin-bottom: 5px; /* Adjust the spacing between legend items */
    }

    .legend-circle {
        display: inline-block;
        width: 10px; /* Adjust the width of the circle */
        height: 10px; /* Adjust the height of the circle */
        border-radius: 50%; /* Make it round */
        margin-right: 5px; /* Adjust the spacing between the circle and the text */
        vertical-align: middle; /* Align vertically with text */
    }
    
</style>  
