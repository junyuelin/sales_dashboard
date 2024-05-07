<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Line Chart</title>
    <script src="https://d3js.org/d3.v6.js"></script>
    <style>
        /* CSS styling */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }

        #chart {
            width: 900px;
            height: 1400px;
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
        <select id="product-select"></select>
    </div>
    <svg id="chart"></svg>

    <script>
        // Fetch data from CSV file and draw line chart
        d3.csv("https://raw.githubusercontent.com/junyuelin/sales_dashboard/main/static/cleaned.csv").then(function(data) {
            // Function to update chart based on selected product
            function updateChart(selectedProduct) {
                d3.select("#chart").selectAll("*").remove();
                // Filter data based on selected product
                var filteredData = data.filter(d => d.Product === selectedProduct);

                // Sum up operating profit by month
                var sumByMonth = d3.rollup(filteredData, v => d3.sum(v, d => +d['Operating Profit']), d => d.Month);

                // Convert data to array for D3.js
                var dataArray = Array.from(sumByMonth, ([key, value]) => ({ Month: key, 'Operating Profit': value }));

                var svg = d3.select("#chart"),
                    margin = { top: 320, right: 20, bottom: 30, left: 50 },
                    width = +svg.attr("width") - margin.left - margin.right,
                    height = +svg.attr("height") - margin.top - margin.bottom,
                    g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

                var x = d3.scaleLinear()
                          .domain([1, 12]) // Assuming months from 1 to 12
                          .range([0, width]);

                var y = d3.scaleLinear()
                          .domain([0, d3.max(dataArray, d => d['Operating Profit'])]) // Scale based on the max profit
                          .range([height, 0]);

                var line = d3.line()
                             .x(d => x(+d.Month))
                             .y(d => y(d['Operating Profit']));

                g.append("g")
                 .attr("transform", "translate(0," + height + ")")
                 .call(d3.axisBottom(x));

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
            var select = d3.select('#product-select');
            productNames.forEach(product => {
                select.append('option')
                      .text(product)
                      .attr('value', product);
            });

            // Initial chart rendering with default selected product
            updateChart(productNames[0]);

            // Event listener for dropdown change
            select.on('change', function() {
                updateChart(this.value);
            });
        });
    </script>
</body>
</html>
