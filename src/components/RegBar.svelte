<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';

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
 
  $: {
    if (data.length > 0) {
      barChart();
    }
  }

  function barChart() {
    const margin = { top: 30, bottom: 30, left: 30, right: 30 };
    const height = 400;
    const width = 600;

    const svg = d3.select('#chart-container')
      .append('svg')
      .attr('width', width)
      .attr('height', height);

    const x = d3.scaleBand()
      .domain(data.filter(d => d.students_in_hh > 0).map(d => d.students_in_hh))
      .range([margin.left, width - margin.right])
      .padding(0.1);

    const y = d3.scaleLinear()
      .domain([0, 1])
      .range([height - margin.bottom, margin.top]);

    svg.append('g')
      .attr('transform', `translate(0, ${height - margin.bottom})`)
      .call(d3.axisBottom(x));

    svg.append('g')
      .attr('transform', `translate(${margin.left}, 0)`)
      .call(d3.axisLeft(y));

    svg.selectAll('.barChart')
      .data(data)
      .join('rect')
      .attr('class', 'barChart')
      .attr('x', d => x(d.students_in_hh))
      .attr('y', d => y(d.receives_meal_plan/d.students_in_hh))
      .attr('width', x.bandwidth())
      .attr('height', d => y(0) - y(d.receives_meal_plan/d.students_in_hh))
      .attr('fill', '#0595b3')
      .on('mouseover', function(b) {
      console.log('Students in household:', b.students_in_hh);
      console.log('Proportion receiving meal plan:', b.receives_meal_plan/b.students_in_hh);
      console.log('Height',y(0) - y(b.receives_meal_plan/b.students_in_hh));
      });
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