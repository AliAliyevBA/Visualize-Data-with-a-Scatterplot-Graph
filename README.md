# Visualize-Data-with-a-Scatterplot-Graph
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Scatterplot Graph</title>
    <script src="https://d3js.org/d3.v7.min.js"></script>
    <style>
        /* Add your CSS styles here */
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }

        h1 {
            margin-bottom: 20px;
        }

        #chart {
            display: block;
            margin: 0 auto;
        }

        .dot {
            fill: steelblue;
        }

        #tooltip {
            position: absolute;
            background-color: lightgray;
            padding: 5px;
            border-radius: 5px;
            display: none;
            font-size: 14px;
        }

        .tick text {
            font-size: 12px;
        }

        #legend {
            font-size: 14px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <h1 id="title">Scatterplot Graph</h1>
    <svg id="chart"></svg>
    <div id="tooltip"></div>
    <div id="legend">Legend: Cyclist data</div>

    <!-- Load the FCC Test Script -->
    <script src="https://cdn.freecodecamp.org/testable-projects-fcc/v1/bundle.js"></script>

    <script>
        const url = "https://raw.githubusercontent.com/freeCodeCamp/ProjectReferenceData/master/cyclist-data.json";

        // Set dimensions for the chart
        const margin = { top: 50, right: 30, bottom: 50, left: 60 };
        const width = 800 - margin.left - margin.right;
        const height = 400 - margin.top - margin.bottom;

        const svg = d3.select("#chart")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
          .append("g")
            .attr("transform", `translate(${margin.left}, ${margin.top})`);

        // Load the data
        d3.json(url).then(data => {

            // Parse data
            data.forEach(d => {
                d.Place = +d.Place;
                d.Seconds = +d.Seconds;
                d.Time = new Date(d.Seconds * 1000); // Convert seconds to Date
                d.Year = +d.Year;
            });

            // Set scales
            const xScale = d3.scaleLinear()
                .domain([d3.min(data, d => d.Year) - 1, d3.max(data, d => d.Year) + 1])
                .range([0, width]);

            const yScale = d3.scaleTime()
                .domain([d3.min(data, d => d.Time), d3.max(data, d => d.Time)])
                .range([0, height]);

            // Define axes
            const xAxis = d3.axisBottom(xScale).tickFormat(d3.format("d"));
            const yAxis = d3.axisLeft(yScale).tickFormat(d3.timeFormat("%M:%S"));

            // Append x-axis
            svg.append("g")
                .attr("id", "x-axis")
                .attr("transform", `translate(0, ${height})`)
                .call(xAxis);

            // Append y-axis
            svg.append("g")
                .attr("id", "y-axis")
                .call(yAxis);

            // Append dots
            svg.selectAll(".dot")
                .data(data)
                .enter()
                .append("circle")
                .attr("class", "dot")
                .attr("cx", d => xScale(d.Year))
                .attr("cy", d => yScale(d.Time))
                .attr("r", 5)
                .attr("data-xvalue", d => d.Year)
                .attr("data-yvalue", d => d.Time)
                .on("mouseover", (event, d) => {
                    const tooltip = d3.select("#tooltip");
                    tooltip.style("display", "block")
                        .style("left", `${event.pageX + 5}px`)
                        .style("top", `${event.pageY - 28}px`)
                        .attr("data-year", d.Year)
                        .html(`Year: ${d.Year}<br>Time: ${d3.timeFormat("%M:%S")(d.Time)}`);
                })
                .on("mouseout", () => {
                    d3.select("#tooltip").style("display", "none");
                });

            // Add legend
            svg.append("text")
                .attr("id", "legend")
                .attr("x", width - 100)
                .attr("y", height - 300)
                .text("Legend: Cyclist data");
        });
    </script>
</body>
</html>
