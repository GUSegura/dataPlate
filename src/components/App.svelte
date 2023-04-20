<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  import Scroll from "./Scrolly.svelte";


  let data_file = "src/EFSA Colombiana_dataset_2022.csv"
  let data = [];
  let currentStep;
  const steps = [
  "<p> 8,513 Colombian households were surveyed</p>",
  '<p>Of these, 47% were households with <span style="font-weight:bold; color:#0595b3;">students</span></p>',
  '<p>49% of the <span style="font-weight:bold; color:#0595b3;">households with students</span> relied on the <span style="font-weight:bold; color:#f46c6c;">school meal plan</span> </p>'];
  let svgWidth = 900;
  let svgHeight = 600;
  let numCols;
  let xScale;
  let yScale;
  let circleSize;

  onMount(async () => {
    try {
      const response = await fetch(data_file);
      const csvData = await response.text();
      data = d3.csvParse(csvData).map((d) => {
        return {
          students_in_hh: (d.nr_escuela_colegio === '_') ? 0 : +d.nr_escuela_colegio,
          recieves_meal_plan: (d.nr_PAE === '_') ? 0 : +d.nr_PAE,
          color: "black"
        };
      });

      data = data.sort((a, b) => b.students_in_hh - a.students_in_hh); 

      console.log(data);
      let numCircles = data.length;
      numCols = Math.ceil(Math.sqrt(numCircles));
      let numRows = Math.ceil(numCircles / numCols);

      let margin = 10;
      circleSize = Math.min(
        (svgWidth - (numCols + 1) * margin) / numCols,
        Math.min((svgHeight - (numRows + 1) * margin) / numRows,100),
      );

      if (circleSize < 0){
        circleSize = 5;
      }

      xScale = d3.scaleLinear()
        .domain([0, numCols - 1])
        .range([margin, svgWidth - margin - circleSize]);
      yScale = d3.scaleLinear()
        .domain([0, numRows - 1])
        .range([margin, svgHeight - margin - circleSize]);
    } catch (error) {
      console.error(error);
    }
  });

  $: {
    updateColors(currentStep);
  }

  function updateColors(step) {
    if (step == 0) {
      data.forEach(d => { d.color = "gray" });
      console.log(step)
    }
    if (step == 1) {
      data.forEach(d => { d.color = d.students_in_hh > 0 ? "#0595b3" : "gray" });
      console.log(step)
    }
    if (step == 2) {
      data.forEach(d => { d.color = d.students_in_hh > 0 ? d.recieves_meal_plan > 0 ? "#f46c6c" : "#0595b3" : "gray" });
      console.log(step)
    }
    coloredData = [...data]; // This line is crucial for Svelte to detect the changes in the data array.
  }

  let coloredData = data;

</script>

<main id="container">
  <section>
    <!-- The chart in the background, which is sticky thanks to CSS below -->
    <div class="chart">
      <svg width={svgWidth} height={svgHeight}>
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

    <!-- The scrolling container which includes each of the text elements -->
    <Scroll bind:value={currentStep}>
      {#each steps as text, i}
        <div class="step" class:active={currentStep === i}>
          <div class="step-content">
            {@html text}
          </div>
        </div>
      {/each}
    </Scroll>
  </section>
</main>

<style>
  #container{
    background: #dbc7a9;

  }
  /* The fixed chart */
  .chart {
    /* background: whitesmoke; */
    width: 900px;
    height: 600px;;
    position: sticky;
    top: 10%;
    margin-left: 30%;
    margin-right: 5%;
  }

  /* Scrollytelling CSS */
  .step {
    height: 80vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    /* background: whitesmoke; */
    color: #ccc;
    border-radius: 5px;
    padding: 0.5rem 1rem;
    display: flex;
    flex-direction: column;
    /* justify-content: center; */
    margin-right: 75%;
    transition: background 500ms ease;
    box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
    z-index: 10;
  }

  .step.active .step-content {
    /* background: white; */
    color: black;
  }
</style>