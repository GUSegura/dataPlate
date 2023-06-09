<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  import { tweened } from 'svelte/motion';
  import { interpolateRgb } from 'd3-interpolate';
  import { onDestroy } from 'svelte';

  export let step;


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
  const circleColors = Array(8513).fill().map(() => 
    tweened('gray', { duration: 800, interpolate: interpolateRgb })
  );

  const tweenedX = tweened(Array(8513).fill().map(() => 0),{ duration: 800});
  const tweenedY = tweened(Array(8513).fill().map(() => 0),{ duration: 800});
  const tweenedSize = tweened(Array(8513).fill().map(() => 0),{ duration: 800});

  let circleColorValues = [];

  const unsubscribes = circleColors.map((circleColor, i) => 
        circleColor.subscribe(value => {
            circleColorValues[i] = value;
        })
  );

  onDestroy(() => {
        unsubscribes.forEach(unsubscribe => unsubscribe());
  });

  function changeColor(c) {
        circleColors.forEach(circleColor => circleColor.set(c));
  }

  onMount(async () => {
    try {  
      // Parse the CSV data and format it for the chart
      data = await d3.csv('https://raw.githubusercontent.com/GUSegura/Colombia_data/master/data.csv');
      data = data.map((d) => {
        return {
          students_in_hh: (d.nr_escuela_colegio === '_') ? 0 : +d.nr_escuela_colegio,
          recieves_meal_plan: (d.nr_PAE === '_') ? 0 : +d.nr_PAE,
          color: "black"
        };
      });

      // Sort the data in descending order of students in households
      data = data.sort((a, b) => b.students_in_hh - a.students_in_hh)
                 .sort((a, b) => b.recieves_meal_plan - a.recieves_meal_plan);
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
      circleSize = 6;
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
      data.forEach(d => { d.color = "gray" });
      filteredData = data;
      let numCircles = filteredData.length;
      updateSize(numCircles);
      changeColor("gray");
      tweenedX.set(data.map((d,i)=>i % numCols));
      tweenedY.set(data.map((d,i)=>Math.floor(i / numCols)));
      tweenedSize.set(data.map((d,i)=>circleSize / 2));

    }
    if (step == 1) {
      data.forEach(d => { d.color = d.students_in_hh > 0 ? "#0595b3" : "gray" });
      filteredData = data;
      let numCircles = filteredData.length;
      updateSize(numCircles);

      circleColors.forEach((color, i) => {
        if (data[i].students_in_hh > 0) {
          color = "#0595b3";
        } else {
          color = "gray";
        }
        if (circleColors[i]) {
          circleColors[i].set(color);
        }
      });
      tweenedX.set(data.map((d,i)=>i % numCols));
      tweenedY.set(data.map((d,i)=>Math.floor(i / numCols)));
      tweenedSize.set(data.map((d,i)=>circleSize / 2));
    }
    if (step == 2) {
      data.forEach(d => { d.color = d.students_in_hh > 0 ? d.recieves_meal_plan > 0 ? "#A491D3" : "#0595b3" : "gray" });
      filteredData = data;
      let numCircles = filteredData.length;
      updateSize(numCircles);

      circleColors.forEach((color, i) => {
        if (data[i].students_in_hh > 0) {
          color = data[i].recieves_meal_plan > 0 ? "#A491D3" : "#0595b3";
        } else {
          color = "gray";
        }
        if (circleColors[i]) {
          circleColors[i].set(color);
        }
      });
      tweenedX.set(data.map((d,i)=>i % numCols));
      tweenedY.set(data.map((d,i)=>Math.floor(i / numCols)));
      tweenedSize.set(data.map((d,i)=>circleSize / 2));
    }
    if (step == 3) {
      data.forEach(d => { d.color = d.recieves_meal_plan > 0 ? "#A491D3" : "#f9f5f1" });
      filteredData = data;
      let numCircles = filteredData.length;
      updateSize(numCircles);

      circleColors.forEach((color, i) => {
        color = data[i].recieves_meal_plan > 0 ? "#A491D3" : "#f9f5f1";
        if (circleColors[i]) {
          circleColors[i].set(color);
        }
      });
      tweenedX.set(data.map((d,i)=>i % numCols));
      tweenedY.set(data.map((d,i)=>Math.floor(i / numCols)));
      tweenedSize.set(data.map((d,i)=>circleSize / 2));
      }
    if (step == 4) {
      filteredData = data.filter(d => d.recieves_meal_plan > 0);
      let numCircles = filteredData.length;
      updateSize(numCircles);

      filteredData.forEach((d, i) => {
        let color = d.recieves_meal_plan > 0 ? "#A491D3" : "#f9f5f1";
        if (circleColors[i]) {
          circleColors[i].set(color);
        }
      });
      tweenedX.set(data.map((d,i)=>i % numCols));
      tweenedY.set(data.map((d,i)=>Math.floor(i / numCols)));
      tweenedSize.set(data.map((d,i)=>circleSize / 2));
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
        cx={xScale($tweenedX[i])}
        cy={yScale($tweenedY[i])}
        r={$tweenedSize[i]}
        fill= {circleColorValues[i]}
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