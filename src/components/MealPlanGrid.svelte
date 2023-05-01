<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';

  export let step;

  let data_file = "src/EFSA Colombiana_dataset_2022.csv"

  let width;
  let height;
  const margin = { top: 30, bottom: 30, left: 30, right: 30 };
  
  // Define the data, number of columns, scales, and circle size
  let data = [];
  let numCols;
  let xScale;
  let yScale;
  let circleSize;
  let filteredData = [];

  onMount(async () => {
    try {

      const response = await fetch(data_file);
      const csvData = await response.text();

      // Parse the CSV data and format it for the chart
      data = d3.csvParse(csvData).map((d) => {
        return {
          students_in_hh: (d.nr_escuela_colegio === '_') ? 0 : +d.nr_escuela_colegio,
          recieves_meal_plan: (d.nr_PAE === '_') ? 0 : +d.nr_PAE,
          fcs_score: (d.fcs === '_') ? 0 : +d.fcs, 
          color: "black"
        };
      }).filter((d) => d.recieves_meal_plan!==0);

      // Sort the data in descending order of students in households
      data = data.sort((a, b) => b.fcs_score - a.fcs_score);
      console.log(data)
  
    } catch (error) {
      console.error(error);
    }
  });

  function updateSize(numCircles){
    // Calculate the number of rows and columns, and circle size for the chart
    numCols = Math.ceil(Math.sqrt(numCircles));
    let numRows = Math.ceil(numCircles / numCols);
    let margin = 10;
    circleSize = Math.min(
      (width - (numCols + 1) * margin) / numCols,
      Math.min((height - (numRows + 1) * margin) / numRows,100),
      );
    
    if (circleSize < 0){
      circleSize = 5;
    }
    else {
      circleSize = 8;
    }
    
    // Define the x and y scales for the chart
    xScale = d3.scaleLinear()
      .domain([0, numCols - 1])
      .range([margin, width - margin - circleSize]);
    yScale = d3.scaleLinear()
      .domain([0, numRows - 1])
      .range([margin, height - margin - circleSize]);
  }

  // Update the colors of the circles based on the step value
  function updateStep(step) {
    if (step == 0) {
      data.forEach(d => { d.color = "#A491D3" });
      filteredData = data;
      let numCircles = filteredData.length;
      updateSize(numCircles);

    }
    if (step == 1) {
      data.forEach(d => { d.color = d.fcs_score > 21 ? d.fcs_score > 35 ? "#54ae89" : "#fcb44c" : "#f46c6c"}); 
      filteredData = data;
      let numCircles = filteredData.length;
      updateSize(numCircles);
    }

    // Copy the data array to the coloredData array to trigger a re-render
    coloredData = [...filteredData];
  }
  
  // Update the colors on every change of the step variable
  $: {
    updateStep(step);
  }

  let coloredData = data;

</script>

<div
class="chart-container"
bind:offsetWidth={width}
bind:offsetHeight={height}
>
  <svg width={width + margin.right + margin.left} {height}>
    {#each coloredData as d, i}
      <circle 
        cx={xScale(i % numCols)}
        cy={yScale(Math.floor(i / numCols))}
        r={circleSize / 2}
        fill= {d.color}
      />
    {/each}
  </svg>
</div>

<style>
  .chart-container {
    height: 80vh;
    max-width: 100%;
        background: #f9f5f1;
        border-radius: 5px;
        box-shadow: 1px 1px 6px #cecece;
  }
</style>