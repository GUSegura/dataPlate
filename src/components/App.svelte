<script>
	/* Template by Connor Rothschild https://twitter.com/CL_Rothschild
	Scrollytelling component from Russell Goldenberg https://twitter.com/codenberg/status/1432774653139984387 */
	
  import Scrolly from "./Scrolly.svelte";
  import ColombiaGrid from "./ColombiaGrid.svelte";
  import RegBar from "./RegBar.svelte";
  import StackedBars from "./StackedBars.svelte";
  import MeealPlanGrid from "./MealPlanGrid.svelte"
  import GradientRect from './GradientRect.svelte';

	
  let value;
  const ColombiaGridSteps = [
  "<h1 style='font-family: Arial; font-size: 30px; line-height: 140%'> <strong>7,097 Colombian households were surveyed </strong> </h1>\
   <p style='font-family: Arial; font-size: 25px; line-height: 160%'> Our data comes from the <strong>Colombian Emergency Food Security and Nutrition Assessment (EFSA)</strong>.</p>\
   <p style='font-family: Arial; font-size: 21px; line-height: 160%; color: #999999'>For more information from the World Food Programme (WFP), <a href='https://docs.wfp.org/api/documents/WFP-0000147247/download/?_ga=2.170658592.1445693605.1682871870-44187498.1677044367'; style='color: #328fcf; text-decoration: none'><strong>click here</strong></a>. </p>"
   ,
  "<p style='font-family: Arial; font-size: 30px; line-height: 160%'>Of these 7,097 households, <span style='font-weight:bold;'>47%</span> were households with <span style='font-weight:bold; color:#0595b3;'>students</span></p>",
  "<p style='font-family: Arial; font-size: 30px; line-height: 140%'><strong>49%</strong> of the <span style='font-weight:bold; color:#0595b3;'>households with students</span> relied on the <span style='font-weight:bold; color:#A491D3;'>school meal plan</span> </p>\
  <p style='font-family: Arial; font-size: 25px; line-height: 160%'>The school meal plan is known as the <strong>Programa de Alimentación Escolar (PAE)</strong>. In 2018, the school meal program consisted of in-school meals provided five times per week for the ten months of the school year.</p>\
  <p style='font-family: Arial; font-size: 21px; line-height: 160%; color: #999999'>For more information on the PAE from the Global Child Nutrition Foundation (GCNF), <a href='https://docs.wfp.org/api/documents/WFP-0000147247/download/?_ga=2.170658592.1445693605.1682871870-44187498.1677044367'; style='color: #328fcf; text-decoration: none'><strong>click here</strong></a>. </p>",
  "<p style='font-family: Arial; font-size: 30px; line-height: 160%'> In total, <strong>1 out of every 5</strong> Colombian households participated in the school meal plan.</p>",
  "<p style='font-family: Arial; font-size: 30px; line-height: 160%'> Examining <strong>access to quality food</strong> and the nutrition of this population provides us with insights into the <strong>effectiveness of the school meal plan.</strong></p>"];

  const MealPlanGridSteps = [
  "<h1 style='font-family: Arial; font-size: 30px; line-height: 140%'> How can the meal plan be improved? </h1> \
    <p style='font-family: Arial; font-size: 25px; line-height: 160%'> We can consider the quality of the diet of <span style='font-weight:bold; color: #A491D3;'>meal plan households</span>.</p>",
  "<h1 style='font-family: Arial; font-size: 30px; line-height: 140%'> The <span style='font-weight:bold;'>food consumption score (FCS)</span> is an indicator of diet diversity, meal frequency, and nutritional value.</h1> \
  <p style='font-family: Arial; font-size: 25px; line-height: 160%'> This index can be used to calculate household food consumption status.</p> \
  <p style='font-family: Arial; font-size: 25px; line-height: 160%'> Households status may be evaluated as <span style='font-weight:bold; color: #54ae89;'>acceptable</span>, <span style='font-weight:bold; color: #fcb44c;'>borderline</span>, or <span style='font-weight:bold; color: #f46c6c;'>poor</span>.</p>"];


  // sources for data: https://globalnutritionreport.org/resources/nutrition-profiles/latin-america-and-caribbean/south-america/colombia/
  // for malnutrition: https://biomedscis.com/fulltext/epidemiology-of-child-malnutrition-in-colombia.ID.000537.php#:~:text=In%20Colombia%2C%20the%20malnutrition%20rate,decreasing%20trend%20in%20recent%20decades.
  
  const BeforeStackedBars = [
  "<h1 style='font-family: Arial; line-height: 140%'>Diets appear unbalanced <strong>regardless of FCS status.</strong></h2><p style='font-family: Arial; font-size: 22px; line-height: 160%'>Even in meal-plan households with 'acceptable' consumption, <strong>many vital food groups are absent.</strong></p>"
];

  const StackedBarsSteps = [
  "<h1 style='font-family: Arial; line-height: 140%; font-size: 30px'>Diets appear unbalanced <strong>regardless of FCS status.</strong></h1>\
    <p style='font-family: Arial; font-size: 25px; line-height: 160%'>Even in meal-plan households with 'acceptable' consumption, <strong>many vital food groups are absent.</strong></p>",

  "<h1 style='font-family: Arial; line-height: 140%; font-size: 30px'><strong>65%</strong> of all households eat vegetables <span style='font-weight:bold; color:#f46c6c;''>less than twice a week</span>.</h1>\
   <p style='font-family: Arial; font-size: 25px; line-height: 160%'>In Colombia, the average adult over 20 years old consumes only 32% of the recommended vegetable intake.</p>\
   <p style='font-family: Arial; font-size: 25px; line-height: 160%'>Despite <a href='https://biomedscis.com/fulltext/epidemiology-of-child-malnutrition-in-colombia.ID.000537.php'; style='color: #328fcf; text-decoration: none'><strong>12.7% of children suffering from malnutrition, </strong></a> the degree to which children meet the recommended vegetable intake, either through school or household meals, is largely missing from the literature.</p>",

  "<h1 style='font-family: Arial; line-height: 160%; font-size: 30px'>Choose a <span style='color:#328fcf;'>food group</span> to learn more about the eating habits of meal-plan households.</h2>\
    <p style='font-family: Arial; font-size: 25px; line-height: 160%'>Hover over the selected <span style='color:#328fcf;'><strong>food group</strong></span> to select between <strong>Vegetables, Meat, Grains</strong> and <strong>Fruit</strong>.</p>"
];

  const RegBarSteps = [
    "<h1 style='font-family: Arial; font-size: 30px; line-height: 140%'>As the number of students in a household increased, so did the proportion of students on the meal plan.</h1>\
    <p style='font-family: Arial; font-size: 25px; line-height: 160%'>Families with more children are generally more reliant on school meal plans.\n </p>\
    <p style='font-family: Arial; font-size: 25px; line-height: 160%'>On average, among families with 7 children, <strong>82.14%</strong> were on the meal plan (versus 48.13% for single-child households).</p>",
    "<h1 style='font-family: Arial; font-size: 30px; line-height: 140%'>However, meal plans do not completely alleviate families from the financial burden of nutrition.</h1>\
    <p style='font-family: Arial; font-size: 25px; line-height: 160%'> Families often turn to <strong>coping strategies</strong> for further financial assistance.</p>\
    <p style='font-family: Arial; font-size: 25px; line-height: 160%'>As the number of children increases, there is a general increased use of coping strategies. On average, families with 7 children reduced adult food consumption <strong>4 of the past 7 days</strong> so that children could eat.</p>",
  ];


  const ConcludingSteps = [
    "<h2>One way to mitigate food insecurity among Colombian youth is by reforming the school meal plans.</h2>\
    <p>In doing so, we can address nutritional deficiencies among food groups like vegetables, and also reduce the financial burden and coping strategies households undergo in providing food.</p>",
  ];

</script>

<section>
    <div class="beginning">
      <div class='hero'>
        <h1 style='font-family: Arial;font-size: 171px;font-weight: 900;'> 
          DataPlate
        </h1>
        <h2 style='font-family: Arial;font-size: 40px;font-weight: 100;'> 
          Adapting the <strong>student meal plan</strong> to the needs of the <strong>Colombian household</strong> through a nutritional lens
        </h2>
      </div>
      <div class="house"></div>
      <div class="intro" style='font-size: 24px;line-height: 160%;'>
        <p> 
          In 2022, the World Food Program (WFP) conducted a food security and nutrition assessment in Colombia. Findings from the report stated that it is critical to maintain and expand emergency assistance to meet the food security needs of the populations identified. The WFP recommended that such assistance should incorporate a <strong>nutritional lens</strong> to make sure vulnerable groups are able to access a nutritious diet. In this data visualization, we take a look at the food security of households with children, focusing on nutrition.
        </p>
      </div>
      <div class="scrollToStart" style='font-size: 25px;font-weight: 200; color: #999999'>
        <p>Scroll to Start</p>
      </div>
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

  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each RegBarSteps as text, i}
        {#if i === 0}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/if} 
        {#if i === 1}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}
              <div class="gradient-rect-container">
                <GradientRect />
              </div>
            </div>
          </div>
    
        {/if}
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

	<div class='hero'>
    <h2 style='font-family: Arial;font-size: 40px;font-weight: 100; align: left'> 
			<strong>We can break down consumption by food groups and FCS status to further analyze household nutrition.</strong>
		</h2>
	</div>
  
  <div class="scrollToStart" style='font-size: 25px;font-weight: 200; color: #999999'>
    <p></p>
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
    <h2 style='font-family: Arial;font-size: 40px;font-weight: 100; align: left; margin-block-start: 45%'> 
			<strong>One way to mitigate food insecurity among Colombian youth is by reforming the school meal plans.</strong>
		</h2>
	</div>
  <div class="outro" style='font-size: 24px;line-height: 160%;'>
    <p> 
      In doing so, we can address nutritional deficiencies among food groups like vegetables, and also reduce the financial burden and coping strategies households undergo in providing food.
    </p>
  </div>
  <div class="scrollToStart" style='font-size: 25px;font-weight: 200; color: #999999'>
    <p></p>
  </div>

<!-- <div class="section-container">
  <div class="steps-container">
    <Scrolly bind:value>
      {#each ConcludingSteps as text, i}
        <div class="step" class:active={value === i}>
          <div class="step-content">{@html text}</div>
        </div>
      {/each}
      <div class="spacer" />
    </Scrolly>
  </div> -->
  <!-- <div class="sticky">
    <StackedBars step={value} />
  </div> -->

</section>

<style>
	:global(body) {
		overflow-x: hidden;
    background-color: #f9f5f1;
	}

  *{
    font-family: Arial;
  }
.beginning {
  display: flex;
  flex-direction: column;
  /* justify-content: center; */
  /* align-items: center; */
  min-height: 100vh;
}

.house {
  align-items: center;
  position: absolute;
  top: 60%;
  right: 16%;
  width: 100px;
  height: 80px;
  transform: translateX(-50%);
  transform: translateY(-50%);
  background-color: black;
  border: 2px solid #000;
  transform: scale(2.7);
  display: flex;
  /* float: right; */

}

.house:before {
  content: "";
  position: absolute;
  top: -65px;
  left: -30px;
  right: auto;
  margin: 0 auto;
  width: 0;
  height: 0;
  border-left: 80px solid transparent;
  border-right: 80px solid transparent;
  border-bottom: 80px solid black;
  background-color: transparent;
  justify-content: center;
  align-items: center;
}

.house:after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  margin: 0 auto;
  width: 50px;
  height: 50px;
  /* background-color: white; */
  border: 2px solid black;
  justify-content: center;
  align-items: center;
}

  
	.hero {
		/* display: flex; */
		place-items: center;
		/* flex-direction: column; */
		justify-content: center;
		text-align: left;
    margin-left: 5%;
    margin-block-end: 2%;
    margin-right: 40%;
	}
	.hero h1 {
		margin-bottom: 0;
    margin-top: 10%;
		font-weight: 200;
	}
	.hero h2 {
		margin-bottom: 0%;
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
    margin-block-end: 17%;
  }

  .step {
    height: 80vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 1rem;
    background: #f9f5f1;
    color: #ccc;
    border-radius: 5px;
    padding: .5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    /* box-shadow: 1px 1px 10px rgba(0, 0, 0, .2); */
    text-align: left;
		width: 75%;
		margin: auto;
		max-width: 500px;
  }

  .intro{
    font-size: 1rem;
    font-family: Arial;
    background: #f9f5f1;
    border-radius: 5px;
    /* padding: .5rem 1rem; */
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    /* box-shadow: 1px 1px 10px rgba(0, 0, 0, .2); */
    text-align: left;
		width: 55%;
    margin-left: 5%;
    margin-block-end: 3%;
  }

  .outro{
    font-size: 1rem;
    font-family: Arial;
    background: #f9f5f1;
    border-radius: 5px;
    /* padding: .5rem 1rem; */
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    /* box-shadow: 1px 1px 10px rgba(0, 0, 0, .2); */
    text-align: left;
    width: 55%;
    margin-left: 5%;
    margin-block-end: 15%;
  }  
  .scrollToStart{
    font-family: Arial;
    /* box-shadow: 1px 1px 10px rgba(0, 0, 0, .2); */
    text-align: center;
    margin-block-end: 10%;

  }
	.step.active .step-content {
		background: #f9f5f1;
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
 .prev-text {
  position: relative;
}

.gradient-rect-container {
  /* left: 50%; */
  display: inline-block;
  justify-content: center;
  align-items: center;
  scale: 1.5;
  margin-left: 20%;
  margin-top: 15%;


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
