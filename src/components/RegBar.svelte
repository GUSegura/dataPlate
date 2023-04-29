<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  export let step;
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
          buy_less: (d.csi_alimentos_menos_preferidos === '_') ? 0 : +d.csi_alimentos_menos_preferidos,
          borrow_food: (d.csi_pedir_prestados_alimentos === '_') ? 0 : +d.csi_pedir_prestados_alimentos,
          reduce_meals: (d.csi_reducir_numero_comidas === '_') ? 0 : +d.csi_reducir_numero_comidas,
          reduce_portions: (d.csi_reducir_tamano_porciones === '_') ? 0 : +d.csi_reducir_tamano_porciones,
          reduce_adult: (d.csi_reducir_adultos === '_') ? 0 : +d.csi_reducir_adultos
        };
      }).filter(d => d.students_in_hh > 0);

    aggData = d3.rollup(data,
    avg => ({
      proportion: d3.mean(avg, d => d.receives_meal_plan / d.students_in_hh),
      buy_less: d3.mean(avg, d => d.buy_less),
      borrow_food: d3.mean(avg, d => d.borrow_food),
      reduce_meals: d3.mean(avg, d => d.reduce_meals),
      reduce_portions: d3.mean(avg, d => d.reduce_portions),
      reduce_adult: d3.mean(avg, d => d.reduce_adult)
    }),
    d => d.students_in_hh
  );
  aggData = Array.from(aggData, ([students_in_hh, values]) => {
    return { students_in_hh, ...values };
  });

    aggData = aggData.sort((a, b) => a.students_in_hh - b.students_in_hh);
    } catch (error) {
        console.error(error);
    }
  });

  
  var colorScale = d3.scaleLinear() 
  .domain([0, 7])
  .range(['white', 'blue']);
 

  $: {
    if (data.length > 0) {
      // updateBars(step);
      barChart();
    }

  function barChart() {
    // console.log('bar chart step',step);
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
    
    
    svg.append('text') //y axis label- Fix
      .attr("class", "y-label")
      .attr("x", -height/2)
      .attr("y", margin.left/2)
      .attr('transform', 'rotate(-90)')
      .style("text-anchor", "middle")
      .text('Proportion of Students Receiving Meal Plan');
    
    svg.selectAll('.barChart')
      .data(aggData)
      .join('rect')
      .attr('class', 'barChart')
      .attr('x', d => x(d.students_in_hh))
      .attr('y', d => y(d.proportion))
      .attr('width', x.bandwidth())
      .attr('height', d => y(0) - y(d.proportion))
      .attr('fill', '#0595b3')
      // .style('fill', d => colorScale(d.reduce_adult))
      .on('mouseover', function(event, d) {
        console.log('Event:',event);
        console.log('Students in household:', d.students_in_hh);
        console.log('Proportion receiving meal plan:', d.proportion); 
        console.log('coping:', d.reduce_adult, color(d.reduce_adult))   
      });

    svg.append('text') //title
      .attr('x', width / 2)
      .attr('y', margin.top)
      .style("text-anchor", "middle")
      .text('Proportion of Students Receiving Meal Plan by Number of Children in Household');
    let condition = true;
    if (condition){ //figure out with scrolly
      csiChart();
    }
  }
}

function csiChart(){
  // console.log('csi step',step);
  d3.selectAll('.barChart')
    .style('fill', d => colorScale(d.reduce_adult));
  // TODO add legend
}

</script>

<div id="chart-container"></div>

<style>
  .barChart {
    height: 80vh;
    max-width: 100%;
  }
  </style> 
