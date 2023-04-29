<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';

  let data_file = "src/EFSA Colombiana_dataset_2022.csv";
  let data = [];
  let aggData = [];

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
      }).filter(d => d.students_in_hh > 0);

      aggData = d3.rollup(data,
          avg => d3.mean(avg, d => d.receives_meal_plan / d.students_in_hh),
          d => d.students_in_hh
      );
      aggData = Array.from(aggData, ([students_in_hh, receives_meal_plan]) => {
        return { students_in_hh, receives_meal_plan };
      });
      aggData = aggData.sort((a, b) => a.students_in_hh - b.students_in_hh);
    } catch (error) {
      console.error(error);
    }
  });

  $: {
    if (data.length > 0) {
      barChart();
    }
  }

  function barChart() {
    const margin = { top: 30, bottom: 30, left: 30, right: 30 };
    const height = 400;// - margin.top - margin.bottom;
    const width = 600;// - margin.left - margin.right;

    const svg = d3.select('#chart-container')
      .append('svg')
      .attr('width', width)
      .attr('height', height);

      const x = d3.scaleBand()
      .domain(aggData.map(d => d.students_in_hh))
      .range([margin.left, width - margin.right])
      .padding(0.1);

    const y = d3.scaleLinear()
      .domain([0, 1])
      .range([height - margin.bottom, margin.top]);

    svg.append('g')
      .attr('transform', `translate(0, ${height - margin.bottom})`)
      .call(d3.axisBottom(x));

    svg.append('text') //x axis label
        .attr('x', width / 2)
        .attr('y', y(0)+margin.bottom)
        .style("text-anchor", "middle")
        .text('Number of Children in Household');

    svg.append('g')
      .attr('transform', `translate(${margin.left}, 0)`)
      .call(d3.axisLeft(y));
    
    
    svg.append('text') //y axis label
      .attr("class", "y-label")
      .attr("x", -height/2)
      .attr("y", margin.left/2)
      .attr('transform', 'rotate(-90)')

      // .attr('dy', '-1em') // move the label up by 1em (the size of the text)
      .style("text-anchor", "middle")
      .text('Proportion of Students Receiving Meal Plan');
    
    // const yLabel = document.querySelector(".y-label");
    // const labelHeight = yLabel.getBBox().height;
    // yLabel.setAttribute("y", 30 - labelHeight / 2);
    
    svg.selectAll('.barChart')
      .data(aggData)
      .join('rect')
      .attr('class', 'barChart')
      .attr('x', d => x(d.students_in_hh))
      .attr('y', d => y(d.receives_meal_plan))
      .attr('width', x.bandwidth())
      .attr('height', d => y(0) - y(d.receives_meal_plan))
      .attr('fill', '#0595b3')
      .on('mouseover', function(event, b) {
        console.log('Event:',event);
        console.log('Students in household:', b.students_in_hh);
        console.log('Proportion receiving meal plan:', b.receives_meal_plan);    
      });

    svg.append('text') //title
      .attr('x', width / 2)
      .attr('y', margin.top)
      .style("text-anchor", "middle")
      .text('Proportion of Students Receiving Meal Plan by Number of Children in Household');
  }

</script>

<div id="chart-container"></div>

<style>
  .barChart {
    height: 80vh;
    max-width: 100%;
  }
  </style> 
