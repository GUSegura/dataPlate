<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';

    function barChart() {
        // set the dimensions and margins of the graph
        var margin = {top: 30, right: 30, bottom: 70, left: 60},
            width = 460 - margin.left - margin.right,
            height = 400 - margin.top - margin.bottom;
        
        // append the svg object to the body of the page
        var svg = d3.select("#my_dataviz")
        .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
        .append("g")
            .attr("transform",
                "translate(" + margin.left + "," + margin.top + ")");
        
        // Parse the Data    
        let data_file = "src/EFSA Colombiana_dataset_2022.csv";
        let data = [];

        onMount(async () => {
            try {
            const response = await fetch(data_file);
            const csvData = await response.text();

            // Parse the CSV data and format it for the chart
            data = d3.csvParse(csvData).map((d) => {
                return {
                students_in_hh: (d.nr_escuela_colegio === '_') ? 0 : +d.nr_escuela_colegio,
                receives_meal_plan: (d.nr_PAE === '_') ? 0 : +d.nr_PAE,
                };
            });
            data = data.sort((a, b) => a.students_in_hh - b.students_in_hh); 
            const uniqueValues = [...new Set(data.map(d => d.students_in_hh))];
            console.log(uniqueValues)
            } catch (error) {
            console.error(error);
            }
        });
    
        
        // X axis
        var x = d3.scaleBand()
        .range([ 0, width ])
        .domain(data.map(function(d) { return d.students_in_hh; }))
        .padding(0.2);
        svg.append("g")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x))
        .selectAll("text")
            .attr("transform", "translate(-10,0)rotate(-45)")
            .style("text-anchor", "end");
        
        // Add Y axis
        var y = d3.scaleLinear()
        .domain([0, 1])
        .range([ height, 0]);
        svg.append("g")
        .call(d3.axisLeft(y));
        
        // Bars
        svg.selectAll("mybar")
        .data(data)
        .enter()
        .append("rect")
            .attr("x", function(d) { return x(d.students_in_hh); })
            .attr("y", function(d) { return y(d.receives_meal_plan/d.students_in_hh); })
            .attr("width", x.bandwidth())
            .attr("height", function(d) { return height - y(d.Value); })
            .attr("fill", "#69b3a2")
    
    
    }
    </script>
    <div id="chart-container"></div>

    <style>
    .barChart {
          height: 80vh;
          max-width: 100%;
              background: linear-gradient(to bottom right, steelblue -100%, white 100%);
              border-radius: 5px;
              box-shadow: 1px 1px 6px #cecece;
        }
    </style>