
<script>
    import Choropleth from "./Choropleth.svelte";
    import Basemap from "./Basemap.svelte";
    import Raster from "./Raster.svelte";
    import BarChart from "./BarChart.svelte";
    import HorizontalBarChart from "./HorizontalBarChart.svelte";
    import Table from "./Table.svelte";
    import GridItem from "./GridItem.svelte";
    import departments from "../geojson/departments.json";
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
        return { index: i , country: "Guatemala", year: 2000}
    })

    const ESlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "El Salvador", year: 2000 }
    })

    const HDlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Honduras", year: 2000}
    })

    const GTlist1=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Guatemala", year: 2021}
    })

    const ESlist1=Array(100).fill().map((_,i)=>{
        return { index: i , country: "El Salvador", year: 2021 }
    })

    const HDlist1=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Honduras", year: 2021}
    })

    let img_intro_src = "images/intro.jpg";
    let img_livelihood = "images/livelihood.jpg";

    // update indices for scrolling here 
    const index_intro = 0;
    const index_deforestation_grid = index_intro + 1; // this is the starting index 
    const index_forest_cover = index_deforestation_grid + 2; // meaning index_deforestation_grid part has two slides 
    const index_food_security = index_forest_cover + 3; 
    const index_table = index_food_security + 3;
    const index_livelihood = index_table + 5;
    const index_migration = index_livelihood + 3;
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

        <div class="chartBackground" style:opacity={index == index_intro ? 1 : 0}>
            <img src={img_intro_src} alt="Deforestation in Honduras" class="image" />
        </div>

        <div class="chartBackground" style:opacity={index == index_deforestation_grid ? 1 : 0}>
            <div class="countryName" style="position: fixed; top: 5vh; left: 25vw; z-index: 99; color: #588157;">
                Forest cover (%) in 2000
            </div>
            <div class="chartBackground">
                <div>
                    <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                        {#each GTlist as item}
                            <GridItem {item}/>
                        {/each}
                    </div>
                    <div class = "countryName">
                        Guatemala 
                    </div>
                    <div class = "countryName" style="color: #588157">
                        70% 
                    </div>
                </div>
                <div>
                    <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                        {#each ESlist as item}
                            <GridItem {item}/>
                        {/each}        
                    </div>
                    <div class = "countryName">
                        El Salvador 
                    </div>
                    <div class = "countryName" style="color: #588157">
                        48% 
                    </div>
                </div>
                <div style="margin-right: 25vw">
                    <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                        {#each HDlist as item}
                            <GridItem {item}/>
                        {/each}
                    </div>
                    <div class = "countryName">
                        Honduras
                    </div>
                    <div class = "countryName" style="color: #588157">
                        60% 
                    </div>
                </div>
            </div>
        </div>

        <div class="chartBackground" style:opacity={index == index_deforestation_grid + 1 ? 1 : 0}>
            <div class="countryName" style="position: fixed; top: 5vh; left: 25vw; z-index: 99; color: #588157;">
                Forest cover (%) in 2021
            </div>
            <div>
                <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                    {#each GTlist1 as item}
                        <GridItem {item}/>
                    {/each}
                </div>
                <div class = "countryName">
                    Guatemala 
                </div>
                <div class = "countryName" style="color: #588157">
                    49% <span style="font-size: 15px; color: red"> (-21%)</span> 
                </div>
            </div>
            <div>
                <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                    {#each ESlist1 as item}
                        <GridItem {item}/>
                    {/each}        
                </div>
                <div class = "countryName">
                    El Salvador 
                </div>
                <div class = "countryName" style="color: #588157">
                    40% <span style="font-size: 15px; color: red"> (-8%)</span> 
                </div>
            </div>
            <div style="margin-right: 25vw">
                <div style = "display: grid; grid-gap: 2px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                    {#each HDlist1 as item}
                        <GridItem {item}/>
                    {/each}
                </div>
                <div class = "countryName">
                    Honduras
                </div><div class = "countryName" style="color: #588157">
                    44% <span style="font-size: 15px; color: red"> (-16%)</span> 
                </div>
            </div>
        </div>

        <Basemap 
            bind:L={L} bind:map={map}
            visible={(index < index_table && index >= index_forest_cover)}
        />
        <Raster
            {L}
            {map}
            url="/raster/treecover2000.png"
            visible={index == index_forest_cover}
            bounds={[[17.8292499999999983, -92.25], [12.971, -83.14575]]}
        />
        <Choropleth 
            visible={index == index_forest_cover + 1}
            domain={[0, 1]}
            {L} 
            {map} 
            data={departments} 
            colorScale={d3.scaleSequential(d3.interpolateGreens)}
            f={(d) => d.COVER_2000 / d.TOTAL_SQUA}
            formatDomain={(d) => (100 * d).toFixed(2) + "%"}
            scaleLabel="% Tree Cover (2000)"
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>Tree Cover (2000): ${(d.COVER_2000 / d.TOTAL_SQUA * 100).toFixed(2)}%</p>`}
        />
        <Choropleth 
            visible={index >= index_forest_cover + 2 && index <= index_food_security + 1}
            {L} 
            {map} 
            data={departments} 
            domain={[0, 0.25]}
            colorScale={d3.scaleSequential(d3.interpolateReds)}
            f={(d) => d.LOSS_10_YE / d.COVER_2000}
            formatDomain={(d) => (100 * d).toFixed(2) + "%"}
            scaleLabel="Forest Cover Loss (2012-2021)"
            highlight={(d) => index <= index_food_security || wfpDepartments.has(d.department_id)}
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>Forest Cover Loss (2012-2021): ${(d.LOSS_10_YE / d.COVER_2000 * 100).toFixed(2)}%</p>`}
        />
        <Choropleth 
            visible={index == index_food_security + 2}
            {L} 
            {map} 
            data={departments} 
            domain={[0, 0.75]}
            colorScale={d3.scaleSequential(d3.interpolateRgb("#fff", "#d33800"))}
            f={(d) => d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE}
            formatDomain={(d) => (100 * d).toFixed(2) + "%"}
            scaleLabel="Food insecurity (%)"
            highlight={(d) => wfpDepartments.has(d.department_id)}
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>% of population facing food insecurity: ${(d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE * 100).toFixed(2)}%</p>`}
        />

        <Table 
            visible={index >= index_table && index <= index_table + 4}
            highlighted = {
                index == index_table || index == index_table + 4 ? [0, 1, 2] : 
                    index == index_table + 1 ? [0] : 
                        index == index_table + 2 ? [1] : [2]
            }
        />

        <div style:opacity={index == index_livelihood ? 1 : 0} style="position: fixed; top: 0; width: 100vw; height: 100vh; transition: opacity 0.4s; align-content: center">
            <img src={img_livelihood} alt="Deforestation and livelihoods" style="height: 100vh; width: 50vw;" />
            <div class="text" style="left: 55vw; width: 40vw; line-height: 1.5; text-align: justify; background-color: azure">
                <h3>Deforestation and its link to loss of livelihoods</h3>
                One quarter of the global population relies on forest for their livelihoods, including disadvantaged communities and indigenous communities. Deforestation in Guatemala, El Salvador, and Honduras can have significant impacts on the livelihoods of people who rely on forests for their well-being and threatens the local population’s livelihoods, incentivizing many of them to seek labor somewhere else.
                <br><br>
                Deforestation can directly cause loss of livelihoods due to impact on industries relying on the forests and indirectly through other consequences of deforestation such as soil erosion and climate change consequences.
            </div>
        </div>

        <div style:opacity={index == index_livelihood + 1 ? 1 : 0} style="position: fixed; top: 0; width: 100vw; height: 100vh; transition: opacity 0.4s; align-content: center">
            <div style="height: 100vh; width: 50vw; overflow: clip;"><img src="/images/direct_forest.png" alt="Deforestation and livelihoods" style="height: 100vh;"/></div>
            <div class="text" style="left: 55vw; width: 40vw; line-height: 1.5; text-align: justify; background-color: azure">
                <h3>Deforestation's direct link to loss of livelihoods</h3>
                The loss of forests can have direct impacts on the availability of natural resources, such as timber and other forest products such as fruits, nuts, and medicinal products. These resources are critical for the livelihoods of people who rely on the sale of these products for their income and sustenance, and hence sustainable forest management and conservation is essential for people working in this sector. 
                <br><br>
                Deforestation can also negatively affect eco-tourism in these regions, directly impacting the livelihoods of people who work in the tourism sector. 
            </div>
        </div>

        <div style:opacity={index == index_livelihood + 2 ? 1 : 0} style="position: fixed; top: 0; width: 100vw; height: 100vh; transition: opacity 0.4s; align-content: center">
            <div style="height: 100vh; width: 50vw; overflow: clip;"><img src="/images/indirect_forest.png" alt="Deforestation and livelihoods" style="width: 50vw;"/></div>
            <div class="text" style="left: 55vw; width: 40vw; line-height: 1.5; text-align: justify; background-color: azure">
                <h3>Deforestation's indirect link to loss of livelihoods through climate change</h3>
                <br><br>
                Deforestation is a significant source of greenhouse gas emissions, which contribute to global warming and climate change, which can in turn disrupt local weather patterns, leading to more frequent and severe natural disasters such as floods and droughts. Floods and droughts can largely impact the agricultural industry which employs large percentages of the population in Guatemala, Honduras, and El Salvador. 
            </div>
        </div>

        <HorizontalBarChart 
            visible={index == index_migration}
            data = {[
                ["For a better job, salary or working conditions", 1239],
                ["Unemployment", 618],
                ["Lack of money to cover other basic needs", 351],
                ["To send remittance", 277],
                ["Lack of money to buy food", 234],
                ["Family reunification", 137],
                ["Unsafety", 118],
                ["For study", 80],
                ["Other", 70],
                ["Deterioration of livelihoods due to natural hazards", 40],
                ["Adventure Tourism", 39],
                ["By the direct impact of a natural hazard", 15],
                ["For health", 15],
                ["Don't Know / No Response", 9],
                ["Domestic violence", 6],
                ["For cultural reasons or custom", 5],
                ["Due to loss of land due to processes of land use change", 4],
            ]}
            title="Reasons for External Migration"
            xAxisLabel="Number of Respondents"
            xTicks={[0, 200, 400, 600, 800, 1000, 1200, 1400]}
            formatXTick={(d) => d.toLocaleString()}
            highlighted={[]}
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
            xAxisLabel="Percent of Respondents"
            xTicks={[0, 0.02, 0.04, 0.06, 0.08, 0.1]}
            formatXTick={(d) => (d * 100) + "%"}
            title="External migration due to the direct impact of a natural hazard, by department"
        />-->

    </div>
  
    <div class="foreground" slot="foreground">
        <section class = "title"> 
            <h1>THE IMPACT OF DEFORESTATION ON CENTRAL AMERICA MIGRATION</h1>
        </section>
        <section> 
            <div class = "text">
                The Northern Central America countries, namely Honduras, Guatemala and El Savador, used to have at least 48% of their total land area covered by forests.
            </div>
        </section>
        <section> 
            <div class = "text">
                Over the two decades from 2000 - 2021, deforestation, both a cause and a consequence of climate change, becomes more prevalent in regions of Honduras, Guatemala and El Savador. 
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
            </div>
        </section>

        <section>
            <div class = "text">
                <h3>Deforestation & Natural Disaster </h3>
                Deforestation leads to excess flooding, along with other natural disasters caused by soil erosion and climate change as a result of deforestation. As trees help to control flooding, tree loss can cause disastrous floods. In 2020, 2 category 4 hurricanes in Central America led to landfall, and as a result, 1.5 million central Americans were displaced.
            </div>
        </section>
        <section>
            <div class = "text">
                In addition, deforestation leads to drought, as the loss of tree coverage decreases a region’s ability to contain moisture. According to United States Institute of Peace, farmers in Central America have experienced multiple droughts since 2014, resulting in crop losses of 70 percent or more during some harvests and often affecting consecutive growing seasons. These natural disasters have caused people to leave their homelands. 
            </div>
        </section>
        <section>
            <div class = "text">
                Adding a graph here 
            </div>
        <section>
            <!-- <div class = "text">
                Deforestation and livelihoods
            </div> -->
        </section>
        <section>
            <!-- <div class = "text">
                Deforestation and livelihoods Pt. 2
            </div> -->
        </section>
        <section>
            <!-- <div class = "text">
                Deforestation and livelihoods Pt. 2
            </div> -->
        </section>
        <section>
            <!-- <div class = "text">
                Deforestation and livelihoods Pt. 2
            </div> -->
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
		background: whitesmoke;
		display:grid;
        grid-gap: 5vw;
        grid-template-columns: auto auto auto;
		height:100vh;
        width:100vw;
		justify-content:center;
		align-items:center;
		position:absolute;
        transition: opacity 0.4s;
	}

    
  
    .foreground {
      height: auto;
      width: 100vw;
      display: flex;
      flex-direction: column;
      align-items:flex-end;
      position: relative;
    }

    .image {
        width: 110vw;
        height:  100vh;
        opacity: .25;
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

    h1 {
        font-size: 3em;
        width: 60vw;
        line-height: 1.5;
    }

    .title {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 90vw;
        text-align: center;
        margin-top: 10vh;
    }

    .countryName {
        font-size: 30px; 
        font-weight: bold; 
        text-align: center; 
        line-height: 2;
        font-family: 'IBM Plex Sans', sans-serif;
    }
</style>
