<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  export let step;
  let width;
  let height;
  let data_file = "src/EFSA Colombiana_dataset_2022.csv";
  let data = [];
  let aggData = [];

  onMount(async () => {
    try {
      data = await d3.csv('https://raw.githubusercontent.com/GUSegura/Colombia_data/master/data.csv');

      // Parse the CSV data and format it for the chart
      data = data.map((d) => {
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
      function barChart() {
        const margin = { top: 30, bottom: 50, left: 30, right: 30 };
        // const height = 700;// - margin.top - margin.bottom;
        // const width = 700;// - margin.left - margin.right;

        let svg = d3.select('.bar-chart-container')
          .append('svg')
          .attr('width', width)
          .attr('height', height);

        let x = d3.scaleBand()
          .domain(aggData.map(d => d.students_in_hh))
          .range([margin.left, width - margin.right])
          .padding(0.4);

        let y = d3.scaleLinear()
          .domain([0, 1])
          .range([height - margin.bottom, margin.top]);

        svg.append('g')
          .attr('transform', `translate(0, ${height - margin.bottom})`)
          .style("font-size", '15px')
          .call(d3.axisBottom(x));

        svg.append('text') //x axis label
            .attr('x', width / 2)
            .attr('y', y(0)+margin.bottom)
            .style("text-anchor", "middle")
            .style("font-size", '20px')
            .text('Number of Children in Household');

        svg.append('g')
          .attr('transform', `translate(${margin.left}, 0)`)
          .style("font-size", '15px')
          .call(d3.axisLeft(y));


        
        svg.selectAll('.barChart')
          .data(aggData)
          .join('rect')
          .attr('class', 'barChart')
          .attr('x', d => x(d.students_in_hh))
          .attr('y', d => y(d.proportion))
          .attr('width', x.bandwidth())
          .attr('height', d => y(0) - y(d.proportion))
          .attr('fill', '#0595b3')
          .on('mouseover', function(event, d) { //tooltip
            d3.select(this).transition() //change opacity when hover
              .duration('400')
              .attr('opacity', '.80')
            let tooltipText = '';
            if (step === 0) {
              tooltipText = `Proportion: ${Number(d.proportion.toFixed(3))}`;
              svg.append('text')
              .attr('class', 'tooltip')
              .attr('id', 'tooltip')
              .attr('x', x(d.students_in_hh) + x.bandwidth() / 2) // center horizontally
              .attr('y', y(d.proportion) - 20) // above the bar
              .attr('text-anchor', 'middle')
              .text(tooltipText);
            } else if (step === 1) {
              
              let tooltipText1 = `Proportion: ${Number(d.proportion.toFixed(3))}`;
              let tooltipText2 = `Coping Score: ${Number(d.reduce_adult.toFixed(3))}`;
              svg.append('text')
                .attr('class', 'tooltip')
                .attr('id', 'tooltip')
                .attr('text-anchor', 'middle')
                .append('svg:tspan')
                .attr('x', x(d.students_in_hh) + x.bandwidth() / 2) // center horizontally
                .attr('y', y(d.proportion) - 30) // above the bar
                .text(tooltipText1)
                .append('svg:tspan')
                .attr('x', x(d.students_in_hh) + x.bandwidth() / 2) // center horizontally
                .attr('y', y(d.proportion) - 10) // above the bar
                .text(tooltipText2);
            }
         })

          .on('mouseout', function() { //normal opacity
            svg.select('.tooltip').remove();
            d3.select(this).transition()
              .duration('400')
              .attr('opacity', '1');
          }); 

        svg.append('text') //title
          .attr('x', width / 2)
          .attr('y', margin.top)
          .style("text-anchor", "middle")
          .style("font-weight", "bold")
          .style("font-size", "22px")
          .text('Proportion of Students Receiving Meal Plan by Number of Children in Household');
      }
      barChart();
    });
     

  var colorScale = d3.scaleSequential(d3.interpolateYlOrRd)
    .domain([0, 7])
    .interpolator(d3.interpolateRgb("#FFFFFF", "#145e90"));
 

  $: {
    console.log("step ",step);

    if (data.length > 0) {
      // barChart();
      updateColors();
    }
  }

function updateColors() {
  console.log("step ",step);
  if (step == 1) { //gradient
    d3.selectAll('.barChart')
      .style('fill', d => colorScale(d.reduce_adult));
     
  } else if (step == 0) {
    d3.selectAll('.barChart')
      .style('fill', '#0595b3');
  }
}

</script>

<div 
  class="bar-chart-container"
  bind:offsetWidth={width}
  bind:offsetHeight={height}>
</div>

<style>
  .bar-chart-container {
    height: 80vh;
    max-width: 100%;
    background: #f9f5f1;
    border-radius: 5px;
    /* box-shadow: 1px 1px 6px #cecece; */
    display: flex;
    justify-content: center;
    align-items: center;
  }

  
  </style> 