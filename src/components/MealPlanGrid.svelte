<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  import { interpolateLab } from 'd3-interpolate';
  import { tweened } from 'svelte/motion';

  export let step;


  let width;
  let height;
  const margin = { top: 30, bottom: 30, left: 30, right: 30 };
  
  // Define the data, number of columns, scales, and circle size
  let data = [];
  let color = [];
  let tweenedColor = tweened(Array(8513).fill('rgb(255, 62, 0)'), {
    duration: 1000,
    interpolate: interpolateLab
  });
  let numCols;
  let xScale;
  let yScale;
  let circleSize;

  onMount(async () => {
    try {
      // Parse the CSV data and format it for the chart
      data = await d3.csv('https://raw.githubusercontent.com/GUSegura/Colombia_data/master/data.csv');
      data = data.map((d) => {
        return {
          students_in_hh: (d.nr_escuela_colegio === '_') ? 0 : +d.nr_escuela_colegio,
          recieves_meal_plan: (d.nr_PAE === '_') ? 0 : +d.nr_PAE,
          fcs_score: (d.fcs === '_') ? 0 : +d.fcs, 
        };
      }).filter((d) => d.recieves_meal_plan!==0);

      // Sort the data in descending order of students in households
      data = data.sort((a, b) => b.fcs_score - a.fcs_score);
      tweenedColor.set(data.map((d)=>'#a491d3'));
      color = data.map((d)=>'#a491d3');
      
      let numCircles = data.length;
      updateSize(numCircles);
  
    } catch (error) {
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
      console.log(step);
      tweenedColor.set(data.map((d)=>'#a491d3'));
      console.log(tweenedColor);
      color = data.map((d)=>'#a491d3')

    }
    if (step == 1) {
      console.log(step);
      tweenedColor.set(data.map((d) => {return d.fcs_score > 21 ? (d.fcs_score > 35 ? '#54ae89' : '#fcb34c') : '#f46c6c';}));
      color = data.map((d) => {return d.fcs_score > 21 ? (d.fcs_score > 35 ? '#54ae89' : '#fcb34c') : '#f46c6c';});
      console.log(tweenedColor);
    }
  }
  
  // Update the colors on every change of the step variable
  $: {
    updateStep(step);
  }

</script>

<div
class="chart-container"
bind:offsetWidth={width}
bind:offsetHeight={height}
>
  <svg width={width + margin.right + margin.left} {height}>
    {#each color as d, i}
      <circle 
        cx={xScale(i % numCols)}
        cy={yScale(Math.floor(i / numCols))}
        r={circleSize / 2}
        fill= {d}
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
        /* box-shadow: 1px 1px 6px #cecece; */
  }
</style>