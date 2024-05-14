
<script>
    import * as d3 from 'd3';
    export let selectedStateData = [];
    console.log('Graph loaded');
    let hovered = -1; 
    let hoveredIndex = -1; // Track the index of the hovered pie slice
    let tooltipVisible = false; // Variable to track tooltip visibility
    let tooltipX = 0; // X-coordinate of the tooltip
    let tooltipY = 0; // Y-coordinate of the tooltip
    
    
    function showTooltip(event, data, index) {
        hoveredIndex = index;
        tooltipVisible = true;
        tooltipX = event.clientX;
        tooltipY = event.clientY +50;
    }

    function hideTooltip() {
        hoveredIndex = -1;
        tooltipVisible = false;
    }
    let arcGenerator = d3.arc()
        .innerRadius(25)
        .outerRadius(140)
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
    <!-- Tooltip -->
    {#if tooltipVisible}
        <div class="tooltip" style="top: {tooltipY}px; left: {tooltipX}px;">
            {arc_data[hoveredIndex].data.Product}: ${((arc_data[hoveredIndex].data.operating_profit / 1000000).toFixed(2))} million
        </div>
    {/if}
    <svg width="630" height="800">
    <!-- container size-->
        <g transform="translate(285, 380)"> // adjust the location of pie chart
            <!-- Place for Pie -->
            {#each arc_data as data, index}
            <path 
                d={arcGenerator({
                    startAngle: data.startAngle,
                    endAngle: data.endAngle
                })}
                fill={index === hovered ? "brown": arc_color(index)}
                stroke="black"
                on:mouseover={(event) => showTooltip(event, data, index)} 
                on:mouseout={hideTooltip} 
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
    {#each arc_data.sort((a, b) => a.data.Product.localeCompare(b.data.Product)) as data, index}
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
    .tooltip {
        position: absolute;
        pointer-events: none;
        background: white;
        border: 1px solid #ddd;
        padding: 5px;
        border-radius: 3px;
        font-size: 1.0rem; /* Set the font size for all tooltips */
        font-family:Roboto, Arial, sans-serif;
    }
    .visualization {
        font: 50px sans-serif;
        margin: auto;
        margin-top: 1px;
        text-align: middle;
    }

    .legend {
    position: absolute;
    top: 570px; /* Adjust the top offset */
    left: 980px; /* Adjust the left offset */
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
    .legend-text {
        /* Add your formatting styles here */
        font-size: 1.0rem;
        font-family:Roboto, Arial, sans-serif;
        }
    
</style>  
