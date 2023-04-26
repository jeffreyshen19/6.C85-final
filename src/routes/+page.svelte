<svelte:head>
	<link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:ital,wght@0,400;0,700;1,400&family=Source+Serif+Pro:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
</svelte:head>

<script>
    import Choropleth from "../components/Choropleth.svelte";
    import Basemap from "../components/Basemap.svelte";
    import Raster from "../components/Raster.svelte";
    import BarChart from "../components/BarChart.svelte";
    import Table from "../components/Table.svelte";
    import GridItem from "../components/GridItem.svelte";
    import departments from "../geojson/departments.json";
    import fi_departments from "../geojson/food_insecurity.json";
    import Scroller from "@sveltejs/svelte-scroller";
    import * as d3 from "d3";

    let L; 
    let map; 

    let count, index, offset, progress;
    let width, height;

    // let forest_loss = {};

    // departments.features.forEach((d) => {
    //     forest_loss[d.properties.department_id] = d.properties.LOSS_10_YE / d.properties.COVER_2000
    // });
    // console.log(forest_loss);
    // let countries = [];
    // console.log(fi_departments.features.map((d) => {
    //     return {
    //         "department_id": d.properties.department_id,
    //         "country": d.properties.country,
    //         "department_name": d.properties.department_name,
    //         "food_insecurity": d.properties.FOOD_INSECURITY_COUNT/ d.properties.SURVEYED_SIZE,
    //         "forest_loss": forest_loss[d.properties.department_id]
    //     }
    // }).filter((d) => !isNaN(d.food_insecurity)));

    const wfpDepartments = new Set(['1184', '901730', 'GT20', '1193', 'GT12', '901742', 'GT13', '901726', 'GT16', '1197', '1185', '901732']);

    const GTlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Guatemala" }
    })

    const ESlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "El Salvador" }
    })

    const HDlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Honduras" }
    })

</script>


<Scroller
    top={0.0}
    bottom={1}
    threshold={0.5}
    bind:count
    bind:index
    bind:offset
    bind:progress
>
    <div
        class="background"
        slot="background"
        bind:clientWidth={width}
        bind:clientHeight={height}
    >
        <div class="progress-bars">
            <p>current section: <strong>{index + 1}/{count}</strong></p>
            <progress value={count ? (index + 1) / count : 0} />

            <p>offset in current section</p>
            <progress value={offset || 0} />

            <p>total progress</p>
            <progress value={progress || 0} />
        </div>

        <div class="chartBackground" style:opacity={index == 0 ? 1 : 0}>
            <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                {#each GTlist as item(item.i)}
                    <GridItem {item}/>
                {/each}
                <div style = "text">
                    Hi
                </div>
            </div>
            <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                {#each ESlist as item(item.i)}
                    <GridItem {item}/>
                {/each}
            </div>
            <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                {#each HDlist as item(item.i)}
                    <GridItem {item}/>
                {/each}
            </div>
        </div>

        <Basemap 
            bind:L={L} bind:map={map}
            visible={index <= 6 && index > 0}
        />
        <Raster
            {L}
            {map}
            url="/raster/treecover2000.png"
            visible={index == 1}
            bounds={[[17.8292499999999983, -92.25], [12.971, -83.14575]]}
        />
        <Choropleth 
            visible={index == 2}
            domain={[0, 1]}
            {L} 
            {map} 
            data={departments} 
            colorScale={d3.scaleSequential(d3.interpolateGreens)}
            f={(d) => d.COVER_2000 / d.TOTAL_SQUA}
            formatDomain={(d) => (100 * d).toFixed(2)}
            scaleLabel="% Tree Cover (2000)"
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>Tree Cover (2000): ${(d.COVER_2000 / d.TOTAL_SQUA * 100).toFixed(2)}%</p>`}
        />
        <Choropleth 
            visible={index >= 3 && index < 6}
            {L} 
            {map} 
            data={departments} 
            domain={[0, 0.25]}
            colorScale={d3.scaleSequential(d3.interpolateReds)}
            f={(d) => d.LOSS_10_YE / d.COVER_2000}
            formatDomain={(d) => (100 * d).toFixed(2)}
            scaleLabel="Forest Cover Loss (2012-2021)"
            highlight={(d) => index < 5 || wfpDepartments.has(d.department_id)}
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>Forest Cover Loss (2012-2021): ${(d.LOSS_10_YE / d.COVER_2000 * 100).toFixed(2)}%</p>`}
        />
        <Choropleth 
            visible={index == 6}
            {L} 
            {map} 
            data={fi_departments} 
            domain={[0, 0.75]}
            colorScale={d3.scaleSequential(d3.interpolateRgb("#fff", "#d33800"))}
            f={(d) => d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE}
            formatDomain={(d) => (100 * d).toFixed(2)}
            scaleLabel="Food insecurity (%)"
            highlight={(d) => wfpDepartments.has(d.department_id)}
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>% of population facing food insecurity: ${(d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE * 100).toFixed(2)}%</p>`}
        />

        <Table 
            visible={index > 6}
            highlighted = {
                index == 7 || index == 11 ? [0, 1, 2] : 
                    index == 8 ? [0] : 
                        index == 9 ? [1] : [2]
            }
        />

        <!-- <BarChart 
            visible={index > 6}
            data={[
                ["Alta Verapaz, GT", 0.05660377358490566],
                ["Yoro, HND", 0.03015075376884422],
                ["Cortes, HND", 0.01694915254237288],
                ["San Marcos, GT", 0.008771929824561403],
                ["Ahuachapan, SLV", 0.0],
                ["Cabañas, SLV", 0.0],
                ["San Salvador, SLV", 0.0],
                ["Usulutan, SLV", 0.0],
                ["Choluteca, HND", 0.0],
                ["Francisco Morazan, HND", 0.0],
                ["Huehuetenango, GT", 0.0],
                ["Chiquimula, GT", 0.0],
            ]}
            highlighted = {[0, 1]}
            yAxisLabel="Percent of Respondents"
            yTicks={[0, 0.02, 0.04, 0.06, 0.08, 0.1]}
            formatYTick={(d) => (d * 100) + "%"}
            title="External migration due to the direct impact of a natural hazard, by department"
        /> -->

    </div>
  
    <div class="foreground" slot="foreground">
        <section> 
            <div class = "text">
                First section
            </div>
        </section>
        <section>
            <div class = "text">
                This map shows tree cover in the Northern Triangle in 2000. As can be seen, the region is <i>heavily</i> forested.
            </div>
        </section>
        <section>
            <div class = "text">
                Here's the same map, visualized on the department level as the percent of overall area covered by trees. Feel free to hover over the map to see more information.
            </div>
        </section>
        <section>
            <div class = "text">
                When looking at deforestation rates (forest loss as a percent of original forest cover), it is clear that deforestation is a major issue impacting the region. Alta Verapaz in Guatemala has lost nearly a quarter of its orgiinal forest cover in the past 10 years!
            </div>
        </section>

        <!-- The maps below show the forest coverage and degree
                of deforestation by region in Central America over time.
                Alta Verapaz, Guatemala, Yoro, Honduras, and Usulutan, 
                El Salvador are among the regions that experienced the
                 most severe deforestation. -->
        <section>
            <div class = "text">
                <h3>Deforestation & Food Security </h3>
                Deforestation reduces the supply of clean drinking water and food for surrounding communities, which leads to food insecurity. According to studies, the highest concentrations of food insecure populations live in regions with tropical forests. Deforestation also destroys the biodiversity and fertility of the land, making it unsustainable to be a reliable food source for people nearby.
            </div>
        </section>
        <section>
            <div class = "text">
                To better understand the relationship between deforestation and food security, we reference survey data from the World Food Program. The survey was only conducted in a few departments in each country, highlighted here. 
            </div>
        </section>
        <section>
            <div class = "text">
                When comparing the deforestation rates to food insecurity, it's clear that heavily deforested areas also have high levels of food insecurity, though food insecurity is an issue across the whole region. 
            </div>
        </section>
        <section>
            <div class = "text">
                Here's a table showing the relationship between deforestation and food insecurity. 
            </div>
        </section>
        <section>
            <div class = "text">
                In Guatemala, Alta Verapaz has both the highest deforestation rates and the highest food insecurity, by a significant degree.
            </div>
        </section>
        <section>
            <div class = "text">
                In Honduras, the most heavily deforested department, Cortés, also has the highest food insecurity rate, though the correlation is less clear in Honduras, as all departments have comparable food insecurity.
            </div>
        </section>
        <section>
            <div class = "text">
                Lastly, in El Salvador, food insecurity is a much more serious issue, though the deforestation rate is low across all departments. 
            </div>
        </section>
        <section>
            <div class = "text">
                In summary, there is some correlation between food insecurity and deforestation, especially in Guatemala, though food insecurity is a major issue across the region, and in areas with low deforestation.
        </section>
    </div>
</Scroller>


<style>
    :global(body){
        padding: 0;
        margin: 0;
    }

    :global(h1, h2, h3, h4, h5, h6){
        font-family: 'Source Serif Pro', serif;
    }

    :global(p){
        font-family: 'IBM Plex Sans', sans-serif;
    }

    .background {
        width: 100vw;
        height: 100vh;
        position: relative;
    }

    .chartBackground{
		background:white;
		display:flex;
        flex-wrap:wrap;	
		flex-direction:column;
		height:100vh;
        width:100vw;
		justify-content:center;
		align-items:center;
		position:absolute;
	}

    
  
    .foreground {
      height: auto;
      position: relative;
      float: right;
    }
  
    .progress-bars {
      position: absolute;
      background: rgba(170, 51, 120, 0.2) /*  40% opaque */;
      visibility: visible;
      z-index: 1000;
    }
  
    section {
      height: 80vh;
      position: relative;
      width: 300px;
      margin-right: 50px;
    }

    .text{
        color: #4d4d4d;
        font-family: Helvetica, Arial;
		font-weight: 200;
        padding: 1em;
        box-sizing: border-box;
        width: 100%;
        background: white;
        box-shadow: 0px 10px 15px -3px rgba(0,0,0,0.1);
        border-radius: 10px;
        top: 50%;
        transform: translateY(-50%);
        position: absolute;
    }
    
    .text h3{
        margin: 0;
        margin-bottom: 5px;
    }
</style>
