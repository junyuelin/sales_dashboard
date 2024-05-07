<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Line Chart</title>
    <script src="https://d3js.org/d3.v7.min.js"></script>
    <style>
        /* CSS styling */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }

        #chart {
            width: 600px;
            height: 400px;
            border: 1px solid #ccc;
        }

        .axis path,
        .axis line {
            fill: none;
            stroke: #000;
            shape-rendering: crispEdges;
        }

        .axis text {
            font-size: 12px;
        }

        .line {
            fill: none;
            stroke: steelblue;
            stroke-width: 2px;
        }
    </style>
</head>
<body>
    <div>
        <label for="product-select">Select Product:</label>
        <select id="product-select">
            <!-- Dropdown options will be populated dynamically -->
            <option value="Men's Street Footwear">Men's Street Footwear</option>
            <option value="Men's Athletic Footwear">Men's Athletic Footwear</option>
            <option value="Women's Street Footwear">Women's Street Footwear</option>
            <option value="Women's Athletic Footwear">Women's Athletic Footwear</option>
            <option value="Men's Apparel">Men's Apparel</option>
            <option value="Women's Apparel">Women's Apparel</option>
        </select>
    </div>
    <svg id="chart"></svg>

    <script>
        // JavaScript code
        // Fetch data from CSV file and draw line chart
        d3.csv("static/cleaned.csv").then(function(data) {
            // Function to update chart based on selected product
            function updateChart(selectedProduct) {
                // Filter data based on selected product
                var filteredData = data.filter(function(d) {
                    return d.Product === selectedProduct;
                });

                // Sum up operating profit by month
                var sumByMonth = d3.rollup(filteredData, v => d3.sum(v, d => d['Operating Profit']), d => d.Month);

                // Convert data to array for D3.js
                var dataArray = Array.from(sumByMonth, ([key, value]) => ({ Month: key, 'Operating Profit': value }));

                // Your D3.js code to draw the line chart goes here
                // For example:
                var svg = d3.select("#chart"),
                    margin = {top: 20, right: 20, bottom: 30, left: 50},
                    width = +svg.attr("width") - margin.left - margin.right,
                    height = +svg.attr("height") - margin.top - margin.bottom,
                    g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

                var parseTime = d3.timeParse("%Y-%m-%d");
                var formatTime = d3.timeFormat("%b %Y");

                var x = d3.scaleTime()
                    .rangeRound([0, width]);

                var y = d3.scaleLinear()
                    .rangeRound([height, 0]);

                var line = d3.line()
                    .x(function(d) { return x(parseTime(d.Month)); })
                    .y(function(d) { return y(+d['Operating Profit']); });

                x.domain(d3.extent(dataArray, function(d) { return parseTime(d.Month); }));
                y.domain(d3.extent(dataArray, function(d) { return +d['Operating Profit']; }));

                g.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(d3.axisBottom(x))
                    .select(".domain")
                    .remove();

                g.append("g")
                    .call(d3.axisLeft(y))
                    .append("text")
                    .attr("fill", "#000")
                    .attr("transform", "rotate(-90)")
                    .attr("y", 6)
                    .attr("dy", "0.71em")
                    .attr("text-anchor", "end")
                    .text("Operating Profit");

                g.append("path")
                    .datum(dataArray)
                    .attr("class", "line")
                    .attr("d", line);
            }

            // Populate dropdown options with unique product names
            var productNames = Array.from(new Set(data.map(d => d.Product)));
            var select = document.getElementById('product-select');
            productNames.forEach(function(product) {
                var option = document.createElement('option');
                option.text = product;
                option.value = product;
                select.add(option);
            });

            // Initial chart rendering with default selected product
            updateChart(productNames[0]);

            // Event listener for dropdown change
            document.getElementById('product-select').addEventListener('change', function() {
                updateChart(this.value);
            });
        });
    </script>
</body>
</html>
