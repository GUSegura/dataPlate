<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  export let step;
  let width;
  let height;
  let data_file = "src/EFSA Colombiana_dataset_2022.csv";
  
  let colombia_dataset = [];
  
  const fcs_count = { 
    total: {
      poor: 0, 
      borderline: 0, 
      acceptable: 0 
    },
    meat: {
      poor: {
        rarely: 0, 
        sometimes: 0, 
        often: 0 
      },
      borderline: {
        rarely: 0, 
        sometimes: 0, 
        often: 0 
      },
      acceptable: {
        rarely: 0, 
        sometimes: 0, 
        often: 0 
      }
    },
    vegetables: {
      poor: {
        rarely: 0, 
        sometimes: 0, 
        often: 0 
      },
      borderline: {
        rarely: 0, 
        sometimes: 0, 
        often: 0 
      },
      acceptable: {
        rarely: 0, 
        sometimes: 0, 
        often: 0 
      }
    }

  };

  onMount(async () => {
    try {

      const response = await fetch(data_file);
      const csvData = await response.text();
      colombia_dataset = d3.csvParse(csvData).map((d) => {
        return {
          fcs_score: (d.fcs === '_') ? 0 : +d.fcs,
          fcs_carne: (d.fcs_carne === '_') ? 0 : +d.fcs_carne,
          fcs_vegetales: (d.fcs_vegetales === '_') ? 0 : +d.fcs_vegetales,
          recieves_meal_plan: (d.nr_PAE === '_') ? 0 : +d.nr_PAE,
          fcs_status: null,
          fcs_status_meat: null,
          fcs_status_vegetables: null
        };
      });

      
  colombia_dataset.forEach(d => { 
      d.fcs_status = d.fcs_score > 21 ? d.fcs_score > 35 ? "acceptable" : "borderline" : "poor" 
      updateFcsCount(fcs_count.total, d.fcs_status);
      d.fcs_status_meat = d.fcs_carne > 3 ? d.fcs_carne > 5 ? "often" : "sometimes" : "rarely" 
      updateFcsCount(fcs_count.meat, d.fcs_status, d.fcs_status_meat);
      d.fcs_status_vegetables = d.fcs_vegetales > 3 ? d.fcs_vegetales > 5 ? "often" : "sometimes" : "rarely" 
      updateFcsCount(fcs_count.vegetables, d.fcs_status, d.fcs_status_vegetables);
    });


  function updateFcsCount(count, status, frequency=false) {
    if (frequency == false) {
      switch (status) {
        case "acceptable":
          count.acceptable += 1;
          break;
        case "borderline":
          count.borderline += 1;
          break;
        default:
          count.poor += 1;
          break;
      }
  }
  else {
    switch (frequency) {
        case "often":
          count[status].often += 1;
          break;
        case "sometimes":
          count[status].sometimes += 1;
          break;
        default:
          count[status].rarely += 1;
          break;
      }
  }

}  

function generateData(food_group){
 let data = [
      { 
        bracket: 'Acceptable', 
        poor: fcs_count[food_group].acceptable.rarely/fcs_count.total.acceptable, 
        borderline: fcs_count[food_group].acceptable.sometimes/fcs_count.total.acceptable, 
        acceptable: fcs_count[food_group].acceptable.often/fcs_count.total.acceptable 
      },

      { 
        bracket: 'Borderline', 
        poor: fcs_count[food_group].borderline.rarely/fcs_count.total.borderline, 
        borderline: fcs_count[food_group].borderline.sometimes/fcs_count.total.borderline, 
        acceptable: fcs_count[food_group].borderline.often/fcs_count.total.borderline 
      },
        
      { 
        bracket: 'Poor', 
        poor: (fcs_count[food_group].poor.rarely/fcs_count.total.poor), 
        borderline: fcs_count[food_group].poor.sometimes/fcs_count.total.poor, 
        acceptable: fcs_count[food_group].poor.often/fcs_count.total.poor 
      }
    ]; 
  return data;
}
 
// console.log("all count information", fcs_count)
// console.log("The data passed in:", data);
function renderChart(food_group) {
      let data = generateData(food_group);
      console.log("The data being used: ", data);
      let svg = d3.select('.chart-container-bars svg');
      svg.selectAll("*").remove();
      
      let stackedData = d3.stack()
        .keys(['poor', 'borderline', 'acceptable'])
        .order(d3.stackOrderNone)
        .offset(d3.stackOffsetNone)(data);
  
      let xScale = d3.scaleBand()
        .domain(data.map(d => d.bracket))
        .range([0, 130])
        .padding(0.4);
  
      let yScale = d3.scaleLinear()
        .domain([0, d3.max(stackedData, d => d3.max(d, d => d[1]))])
        .range([60, 0]);
  
      let color_pal = ['#d8b365', '#63cfae', '#328fcf']; //yellow, green,blue  
      let colors = d3.scaleOrdinal()
        .domain(['poor', 'borderline', 'acceptable'])
        .range(color_pal);
  
      svg.selectAll('g')
        .data(stackedData)
        .enter().append('g')
          .attr('fill', d => colors(d.key))
        .selectAll('rect')
        .data(d => d)
        .enter().append('rect')
          .attr('x', d => xScale(d.data.bracket))
          .attr('y', d => yScale(d[1]))
          .attr('height', d => yScale(d[0]) - yScale(d[1]))
          .attr('width', xScale.bandwidth())
          .on('mouseover', function(d) {
            d3.select(this).transition()
              .duration('400')
              .attr('opacity', '.80');
            // uncomment for tooltip on hover:

            // d3.select(this)
            //   .append('title')
            //   .text('placeholder');
          })
          .on('mouseout', function() {
            d3.select(this).transition()
              .duration('400')
              .attr('opacity', '1');
            // d3.select(this).select('title').remove();
          }); 
          svg.append("g")
            .attr("transform", "translate(0, 58)") // Position at the bottom of the chart
            .call(d3.axisBottom(xScale))
            .selectAll("text")
              .style("font-size", "3.0px") 
              .attr("y", 5) // Move the text down a bit
              .style('fill', '#333333')
              .style('font-family', 'Arial')
              .style("font-weight", "bold")
              .attr("text-anchor", "middle");
          svg.selectAll(".domain, .tick line").remove();
// legend:
          let y_pos_l = -16; // closer to 0 is lower
          let square_size = 2;
          let spacing = 3.3;
          let text_space = 1.1;

          svg.append("rect")
            .attr("x",100)
            .attr("y",y_pos_l)
            .attr("font", "sans-serif")
            .attr("width", square_size)
            .attr("height", square_size)
            .style("fill", color_pal[2])
            // .attr("transform", "translate(0, 100)")            
          svg.append("text")
            .attr("x", 104)
            .attr("y", y_pos_l + text_space)
            .attr("alignment-baseline","middle")
            .text("6 - 7 days")
            .style("font-size", "2.3px")
            .style('fill', '#444444')
            .style('font-family', '"Open Sans", sans-serif')

          svg.append("rect")
            .attr("x",100)
            .attr("y",y_pos_l + spacing)
            .attr("font", "sans-serif")
            .attr("width", square_size)
            .attr("height", square_size)
            .style("fill", color_pal[1])
            // .attr("transform", "translate(0, 100)")            
          svg.append("text")
            .attr("x", 104)
            .attr("y", y_pos_l + spacing + text_space)
            .attr("alignment-baseline","middle")
            .text("3 - 5 days")
            .style("font-size", "2.3px")
            .style('fill', '#444444')
            .style('font-family', '"Open Sans", sans-serif')

          svg.append("rect")
            .attr("x",100)
            .attr("y",y_pos_l + 2*spacing)
            .attr("font", "sans-serif")
            .attr("width", square_size)
            .attr("height", square_size)
            .style("fill", color_pal[0])
            // .attr("transform", "translate(0, 100)")            
          svg.append("text")
            .attr("x", 104)
            .attr("y", y_pos_l + 2*spacing + text_space)
            .attr("alignment-baseline","middle")
            .text("0 - 2 days")
            .style("font-size", "2.3px")
            .style('fill', '#444444')
            .style('font-family', '"Open Sans", sans-serif')                        
            
            // .attr("transform", "translate(0, 100)")
  

              
}
  //replace to choose default food group            
  renderChart("vegetables");

  document.querySelectorAll('input[type="radio"]').forEach(function(radio) {
  radio.addEventListener('change', function(event) {
    renderChart(event.target.value);
  });
});
      } catch (error) {
        console.error(error);
      }
    });
  
  </script>
  
  <div
  class="chart-container-bars"
  bind:offsetWidth={width}
  bind:offsetHeight={height}
>
  <svg width="100%" height="auto" viewBox="0 0 130 48"></svg> 
</div>

<input type="radio" name="food_group" value="vegetables" id="vegetables" checked="checked">
<label for="vegetables">Vegetables</label>
<br>
<input type="radio" name="food_group" value="meat" id="meat">
<label for="meat">Meat</label>

<style>
  .chart-container-bars {
    height: 80vh;
    max-width: 100%;
        background: linear-gradient(to bottom right, #dbc7a9 -100%, white 100%);
        border-radius: 5px;
        box-shadow: 1px 1px 6px #cecece;
  }
    
  .chart-container-bars svg {
    transform: translate(-50%, -50%);
    position: absolute;
    top: 48%;
    left: 50%;
  }    
</style>