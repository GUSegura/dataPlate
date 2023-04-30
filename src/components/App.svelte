<script>
	/* Template by Connor Rothschild https://twitter.com/CL_Rothschild
	Scrollytelling component from Russell Goldenberg https://twitter.com/codenberg/status/1432774653139984387 */
	
  import Scrolly from "./Scrolly.svelte";
  import ColombiaGrid from "./ColombiaGrid.svelte";
  import RegBar from "./RegBar.svelte";
  import StackedBars from "./StackedBars.svelte";
  import MeealPlanGrid from "./MealPlanGrid.svelte"
	
  let value;
  const ColombiaGridSteps = [
  "<p style='font-family: Arial; font-size: 22px; line-height: 160%'> 7,097 Colombian households were surveyed in the <a href='https://docs.wfp.org/api/documents/WFP-0000147247/download/?_ga=2.170658592.1445693605.1682871870-44187498.1677044367'>Colombian Emergency Food Security and Nutrition Assessment (EFSA)</a> </p>",
  "<p style='font-family: Arial; font-size: 22px; line-height: 160%'>Of these, <span style='font-weight:bold;'>47%</span> were households with <span style='font-weight:bold; color:#0595b3;'>students</span></p>",
  "<p style='font-family: Arial; font-size: 22px; line-height: 160%'>49% of the <span style='font-weight:bold; color:#0595b3;'>households with students</span> relied on the <span style='font-weight:bold; color:#A491D3;'>school meal plan</span> </p>",
  "<p style='font-family: Arial; font-size: 22px; line-height: 160%'> That is, 1 out of every 5 Colombian households participated in the school meal plan.</p>",
  "<p style='font-family: Arial; font-size: 22px; line-height: 160%'> Focusing on the food security and nutrition of this population provides us with insights into...</p>"];

  const MealPlanGridSteps = [
  "<p style='font-family: Arial; font-size: 22px; line-height: 160%'> How can the meal plan be improved? </p> <p style='font-family: Arial; font-size: 22px; line-height: 160%'> We can consider the quality of the diet of <span style='font-weight:bold; color: #A491D3;'>meal plan households</span></p>",
  "<p style='font-family: Arial; font-size: 22px; line-height: 160%'> The <span style='font-weight:bold;'>food consumption score (FCS)</span> is an indicator of diet diversity, meal frequency, and nutritional value.</p> <p style='font-family: Arial; font-size: 22px; line-height: 160%'> This index can be used to calculate household food consumption status.</p> <p style='font-family: Arial; font-size: 22px; line-height: 160%'> Households status may be evaluated as <span style='font-weight:bold; color: #54ae89;'>acceptable</span>, <span style='font-weight:bold; color: #fcb44c;'>boderline</span>, or <span style='font-weight:bold; color: #f46c6c;'>poor</span>.</p>"];


  // sources for data: https://globalnutritionreport.org/resources/nutrition-profiles/latin-america-and-caribbean/south-america/colombia/
  // for malnutrition: https://biomedscis.com/fulltext/epidemiology-of-child-malnutrition-in-colombia.ID.000537.php#:~:text=In%20Colombia%2C%20the%20malnutrition%20rate,decreasing%20trend%20in%20recent%20decades.
  const StackedBarsSteps = [
  "<h1 style='font-family: Arial; line-height: 140%'>Diets appear unbalanced <strong>regardless of FCS status.</strong></h2><p style='font-family: Arial; font-size: 22px; line-height: 160%'>Even in meal-plan households with 'acceptable' consumption, <strong>many vital food groups are absent.</strong></p>",

  "<h2 style='font-family: Arial; line-height: 160%'><strong>65%</strong> of all households eat vegetables <span style='font-weight:bold; color:#ba9a56;''>less than twice a week</span>.</h2>\
   <p style='font-family: Arial; font-size: 22px; line-height: 160%'>In Colombia, the average adult over 20 years old consumes only 32% of the recommended vegetable intake.</p>\
   <p style='font-family: Arial; font-size: 22px; line-height: 160%'>Despite <strong>13% of children suffering from malnutrition</strong>, the degree to which children meet the recommended vegetable intake is largely missing from the literature.</p>",
  
  "<h2 style='font-family: Arial; line-height: 160%'>Choose a food group to learn more about the eating habits of meal-plan households.</h2>"
];

  const steps = [
		 "<p>This is a dynamic, responsive scatterplot that uses Russell Goldenberg's <a href='	https://twitter.com/codenberg/status/1432774653139984387' target='_blank'><code>Scrolly</code></a> to update its points' values on scroll.</p>",
    "<p>The scatterplot uses tweened values to automatically update your points with smooth transitions. It also binds to the width of the container <code>div</code>, so its responsive by default.</p>",
    "<p>Try resizing me to see the 'side-by-side' version, compared to the 'text-on-top' version that appears on small screens.</p><p>Want it to always appear 'text-on-top'? Just comment out the media query at the bottom of our styles (as in, leave the styles but comment out the surrounding <code>media</code> query).</p>",
  ];

  const RegBarSteps = [
    "<p>regular bar1  </p>",
    "<p>regular bar2 </p>",
  ]
</script>

<section>
	<div class='hero'>
		<h1 style='font-family: Arial; line-height: 160%'> 
			DataPlate
		</h1>
    <h2 style='font-family: Arial; line-height: 160%'> 
			Examining the Nutrition of Columbian Youth 
		</h2>
	</div>
  <div class="intro">
    <p> 
      In 2022, the World Food Program (WFP) conducted a food security and nutrition assessment in Colombia. Findings from the report stated that it is critical to maintain and expand emergency assistance to meet the food security needs of the populations identified. The WFP recommended that such assistance should incorporate a nutritional lens to make sure vulnerable groups are able to access a nutritious diet. 
    </p>
    <p>
      In this data visualization, we take a look at the food security of households with children with a focus on nutrition.
    </p>
    <p style="font-weight:bold; justify-content: center;">
      Scroll down to start.
    </p>
  </div>

  <!-- This module is used as a container for the dataViz. Add a file for your data viz and -->
  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each ColombiaGridSteps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="sticky">
      <ColombiaGrid step={value} />
    </div>
  </div>

<!-- AZ -->
  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each RegBarSteps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="sticky">
      <RegBar step={value} />
    </div>
  </div>


  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each MealPlanGridSteps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="sticky">
      <MeealPlanGrid step={value} />
    </div>
  </div>

  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each StackedBarsSteps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="sticky">
      <StackedBars step={value} />
    </div>
  </div>

	<div class='hero'>
		<h1> 
			Thanks!
		</h1>
		<h2>
			<a href='https://twitter.com/CL_Rothschild' target="_blank">Questions and Tips</a>
		</h2>
	</div>
</section>

<style>
	:global(body) {
		overflow-x: hidden;
	}
	
	.hero {
		height: 60vh;
		display: flex;
		place-items: center;
		flex-direction: column;
		justify-content: center;
		text-align: left;
	}
	
	.hero h2 {
		margin-top: 0;
		font-weight: 200;
	}
	
  .spacer {
    height: 40vh;
  }

  .sticky {
    position: sticky;
    top: 10%;
		flex: 1 1 60%;
    width: 60%;
  }

  .section-container {
    margin-top: 1em;
    text-align: center;
    transition: background 100ms;
    display: flex;
  }

  .step {
    height: 80vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 1rem;
    background: whitesmoke;
    color: #ccc;
    border-radius: 5px;
    padding: .5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    box-shadow: 1px 1px 10px rgba(0, 0, 0, .2);
    text-align: left;
		width: 75%;
		margin: auto;
		max-width: 500px;
  }

  .intro{
    font-size: 1rem;
    font-family: Arial;
    background: whitesmoke;
    border-radius: 5px;
    padding: .5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    box-shadow: 1px 1px 10px rgba(0, 0, 0, .2);
    text-align: left;
		width: 75%;
		margin: auto;
  }

	.step.active .step-content {
		background: white;
		color: black;
	}
	
  .steps-container,
  .sticky {
    height: 100%;
  }

  .steps-container {
    flex: 1 1 40%;
    z-index: 10;
  }
	
/* Comment out the following line to always make it 'text-on-top' */
  @media screen and (max-width: 768px) {
    .section-container {
      flex-direction: column-reverse;
    }
    .sticky {
      width: 95%;
			margin: auto;
    }
  }
</style>
