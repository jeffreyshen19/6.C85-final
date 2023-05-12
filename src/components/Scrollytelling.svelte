
<script>
    import Choropleth from "./Choropleth.svelte";
    import Basemap from "./Basemap.svelte";
    import Raster from "./Raster.svelte";
    import BarChart from "./BarChart.svelte";
    import HorizontalBarChart from "./HorizontalBarChart.svelte";
    import Table from "./Table.svelte";
    import GridItem from "./GridItem.svelte";
    import departments from "../geojson/departments.json";
    import countries from "../geojson/countries.json";
    import Scroller from "@sveltejs/svelte-scroller";
    import * as d3 from "d3";

    let L; 
    let map; 

    export let index;
    export let progress;
    export let offset; 
    let count;
    let width, height;

    const wfpDepartments = new Set(['1184', '901730', 'GT20', '1193', 'GT12', '901742', 'GT13', '901726', 'GT16', '1197', '1185', '901732']);

    const GTlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Guatemala", year: 2000, type: "tree" }
    })

    const ESlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "El Salvador", year: 2000, type: "tree" }
    })

    const HDlist=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Honduras", year: 2000, type: "tree" }
    })

    const GTlist1=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Guatemala", year: 2021, type: "tree" }
    })

    const ESlist1=Array(100).fill().map((_,i)=>{
        return { index: i , country: "El Salvador", year: 2021, type: "tree" }
    })

    const HDlist1=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Honduras", year: 2021, type: "tree" }
    })

    const GTlist2=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Guatemala", year: 2021, type: "people" }
    })

    const ESlist2=Array(100).fill().map((_,i)=>{
        return { index: i , country: "El Salvador", year: 2021, type: "people" }
    })

    const HDlist2=Array(100).fill().map((_,i)=>{
        return { index: i , country: "Honduras", year: 2021, type: "people" }
    })

    let img_intro_src = "images/intro.jpg";
    let img_livelihood = "images/livelihood.jpg";
    let img_intro_text = "deforestation_intro.jpeg";

    // Choropleth attributes 
    const index_deforestation_grid = 0; // this is the starting index 
    const index_forest_cover = index_deforestation_grid + 2; // meaning index_deforestation_grid part has two slides 
    const index_food_security = index_forest_cover + 3; 
    const index_table = index_food_security + 3;
    const index_livelihood = index_table + 5;
    const index_natural_disaster = index_livelihood + 4; 
    const index_migration = index_natural_disaster + 4;
    const index_conclusion = index_migration + 1;

    let choropleths = {};
    choropleths[index_forest_cover + 1] = {
        "colorScale": d3.scaleSequential(d3.interpolateGreens),
        "f": (d) => d.COVER_2000 / d.TOTAL_SQUA,
        "domain": [0, 1],
        "formatDomain": (d) => (100 * d).toFixed(2) + "%",
        "scaleLabel": "% Tree Cover (2000)",
        "highlight": (d) => true,
        "renderTooltip": (d) => `<h1>${d.department_name}, ${d.country}</h1><p>Tree Cover (2000): ${(d.COVER_2000 / d.TOTAL_SQUA * 100).toFixed(2)}%</p>`
    };
    choropleths[index_forest_cover + 2] = {
        "colorScale": d3.scaleSequential(d3.interpolateReds),
        "f": (d) => d.LOSS_10_YE / d.COVER_2000,
        "domain": [0, 0.25],
        "formatDomain": (d) => (100 * d).toFixed(2) + "%",
        "scaleLabel": "Forest Cover Loss (2012-2021)",
        "highlight": (d) => index <= index_food_security || wfpDepartments.has(d.department_id),
        "renderTooltip": (d) => `<h1>${d.department_name}, ${d.country}</h1><p>Forest Cover Loss (2012-2021): ${(d.LOSS_10_YE / d.COVER_2000 * 100).toFixed(2)}%</p>`
    };
    choropleths[index_food_security] = choropleths[index_forest_cover + 2];
    choropleths[index_food_security + 1] = choropleths[index_forest_cover + 2];
    choropleths[index_food_security + 2] = {
        "colorScale": d3.scaleSequential(d3.interpolateRgb("#fff", "#5a228b")),
        "f": (d) => d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE,
        "domain": [0, 0.75],
        "formatDomain": (d) => (100 * d).toFixed(2) + "%",
        "scaleLabel": "Food insecurity (%)",
        "highlight": (d) => wfpDepartments.has(d.department_id),
        "renderTooltip": (d) => `<h1>${d.department_name}, ${d.country}</h1><p>% of population facing food insecurity: ${!wfpDepartments.has(d.department_id) ? "No data" : (d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE * 100).toFixed(2) + "%"}</p>`
    };

    let numFloodMap = {
        "colorScale": d3.scaleSequential(d3.interpolateBlues),
        "f": (d) => d.FLOOD_NUM,
        "domain": [0, 30], 
        "formatDomain":  (d) => d, 
        "scaleLabel": "Number of floods from 2008 - 2021",
        "highlight": (d) => true, 
        "renderTooltip": (d) => `<h1>${d.COUNTRY}</h1>\nNumber of floods from 2008 - 2021: ${d.FLOOD_NUM}`
    }

    let displacementFloodMap = {
        "colorScale": d3.scaleSequential(d3.interpolateBlues),
        "f": (d) => d.DISPLACED,
        "domain": [0, 200000], 
        "formatDomain":  (d) => d, 
        "scaleLabel": "Number of people displaced by floods from 2008 - 2021",
        "highlight": (d) => true, 
        "renderTooltip": (d) => `<h1>${d.COUNTRY}</h1>\nNumber of people displaced by floods (2008 - 2021): ${d.DISPLACED}`
    }
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

        <p class = "attribution">
            {#if index <= 2}
                Forest cover data from <a href = "https://data.globalforestwatch.org/documents/a400422d410b4c158f499b5dbf7a7c66/explore">Global Forest Watch</a>.
            {:else if index <= 11}
                Forest cover data from <a href = "https://data.globalforestwatch.org/documents/a400422d410b4c158f499b5dbf7a7c66/explore">Global Forest Watch</a>. Food insecurity data from the World Food Program.
            {:else if index == 19 || index == 18}
                Data from the <a href = "https://www.fao.org/faostat/en/#data/WCAD">Food and Agriculture Organization of the United Nations</a>. 
            {/if}
        </p>

        <p class = "map-attribution">
            {#if index >= 2 && index < 7}
                Forest cover data from <a href = "https://data.globalforestwatch.org/documents/a400422d410b4c158f499b5dbf7a7c66/explore">Global Forest Watch</a>.
            {:else if index == 7}
                Forest cover data from <a href = "https://data.globalforestwatch.org/documents/a400422d410b4c158f499b5dbf7a7c66/explore">Global Forest Watch</a>. Food insecurity data from the World Food Program.
            {:else if index == 19 || index == 20}
                Data from the <a href = "https://www.internal-displacement.org/database/displacement-data">Global Internal Displacement Database</a>.
            {/if}
        </p>

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
            visible={(index < index_table && index >= index_forest_cover) || (index == index_natural_disaster + 2) || (index == index_natural_disaster + 3)}
        />
        <Raster
            {L}
            {map}
            url="/raster/treecover2000.png"
            visible={index == index_forest_cover}
            bounds={[[17.8292499999999983, -92.25], [12.971, -83.14575]]}
        />
        <Choropleth 
            {L} 
            {map} 
            data={countries} 
            visible={index == index_natural_disaster + 2 || index  == index_natural_disaster + 3} 
            {...(index == index_natural_disaster + 2 ? numFloodMap : displacementFloodMap)}       
        />
        <Choropleth 
            {L} 
            {map} 
            data={departments} 
            visible={(index > index_forest_cover && index < index_table)} 
            {...(index in choropleths ? choropleths[index] : choropleths[index <= index_forest_cover ? index_forest_cover + 1: index_table - 1])}       
        />

        
        

        <Table 
            visible={index >= index_table && index <= index_table + 4}
            highlighted = {
                index == index_table || index == index_table + 4 ? [0, 1, 2] : 
                    index == index_table + 1 ? [0] : 
                        index == index_table + 2 ? [1] : [2]
            }
        />

        <div class="chartBackground" style:opacity={index == index_natural_disaster + 1 ? 1 : 0}>
            <div class="countryName" style="position: fixed; top: 5vh; left: 8vw; z-index: 99; color: #588157;">
                Share of employment in agriculture, forestry and fishing (%) in 2021
            </div>
            <div class="chartBackground" style="position: fixed; top: 5vh; left: 0vw; z-index: 99; background: transparent; width: 100vw;">
                <div>
                    <div style = "display: grid; grid-gap: 2px; row-gap: 15px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                        {#each GTlist2 as item}
                            <GridItem {item}/>
                        {/each}
                    </div>
                    <div class = "countryName">
                        Guatemala 
                    </div>
                    <div class = "countryName" style="color: #588157">
                        29.2% 
                    </div>
                </div>
                <div>
                    <div style = "display: grid; grid-gap: 2px; row-gap: 15px; grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                        {#each ESlist2 as item}
                            <GridItem {item}/>
                        {/each}        
                    </div>
                    <div class = "countryName">
                        El Salvador 
                    </div>
                    <div class = "countryName" style="color: #588157">
                        15.2% 
                    </div>
                </div>
                <div style="margin-right: 25vw">
                    <div style = "display: grid; grid-gap: 2px; row-gap: 15px;  grid-template-columns:auto auto auto auto auto auto auto auto auto auto;">
                        {#each HDlist2 as item}
                            <GridItem {item}/>
                        {/each}
                    </div>
                    <div class = "countryName">
                        Honduras
                    </div>
                    <div class = "countryName" style="color: #588157">
                        24.8% 
                    </div>
                </div>
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
                ["Loss of land due to processes of land use change", 4],
            ]}
            title="Reasons for External Migration"
            xAxisLabel="Number of Respondents"
            caption="External migration is defined as leaving the home country for another country. Data from the World Food Program."
            xTicks={[0, 200, 400, 600, 800, 1000, 1200, 1400]}
            formatXTick={(d) => d.toLocaleString()}
            highlighted={[]}
        /> 
        <HorizontalBarChart 
            visible={index == index_migration + 1}
            data = {[
                ["For a better job, salary or working conditions", 438],
                ["Unemployment", 174],
                ["Other", 117],
                ["Lack of money to cover basic needs (besides food)", 80],
                ["Lack of money to buy food", 74],
                ["Adventure Tourism", 53],
                ["For study", 50],
                ["Don't Know / No Response", 45],
                ["For health", 31],
                ["Deterioration of livelihoods due to natural hazards", 13],
                ["Unsafety", 5],
                ["By the direct impact of a natural hazard", 4],
                ["Loss of land due to processes of land use change", 2],
            ]}
            title="Reasons for Internal Migration"
            caption="Internal migration is defined as relocating from one region in the home country to another region. Data from the World Food Program."
            xAxisLabel="Number of Respondents"
            xTicks={[0, 200, 400, 600]}
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

    <div class="chartBackground" style:opacity={index == index_conclusion}>
        <img src={"\image\deforestation_intro.jpeg"} alt="conclusion" class="image" />
    </div>

    <div class="foreground" slot="foreground">
        <section id="deforestation"> 
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
                Here's the same map, visualized on the department level as the percent of overall area covered by trees. 
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
        <section id="food-insecurity">
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
        </section>
        <section style="top: -70vh; left: 5vw; width: 100vw; display:flex; justify-content: center; background: white;">
            <div class = "paragraph">
                <img src="images/forest.jpg" style="width: 600px" alt="Forest in Honduras">
                <br><br>
                <h3 style="text-align: center;">Deforestation and its link to loss of livelihoods</h3>
                <br><br>
                One quarter of the global population relies on forest for their livelihoods, including disadvantaged communities and indigenous communities. Deforestation in Guatemala, El Salvador, and Honduras can have significant impacts on the livelihoods of people who rely on forests for their well-being and threatens the local population’s livelihoods, incentivizing many of them to seek labor somewhere else.
                <br><br>
                Deforestation can directly cause loss of livelihoods due to impact on industries relying on the forests and indirectly through other consequences of deforestation such as soil erosion and climate change consequences.
            </div>
        </section>

        <section style="left: 5vw; width: 100vw; display:flex; justify-content: center;">
            <div class = "paragraph">
                <h3 style="text-align: center;">Deforestation's direct link to loss of livelihoods</h3><br><br>
                The loss of forests can have direct impacts on the availability of natural resources, such as timber and other forest products such as fruits, nuts, and medicinal products. These resources are critical for the livelihoods of people who rely on the sale of these products for their income and sustenance, and hence deforestation can directly impact the livelihoods of these people. Hover on the images below to learn more.
            </div>
        </section>
        
        <section style="pointer-events:all; width: 100vw;height: 100vh; top: -30vw; left: 3vw;display: flex; justify-content: center; align-items: center;">
            <div style="width: 100vw; display: flex; align-items: center; justify-content: center;">
                <div class = container>
                    <div class = card>
                        <div class = cardImage>
                        <img src="/images/timber.webp" alt="People walking through a Guatamalan forest">
                        </div>
                        <div class = content>
                        <h3>Timber harvesting</h3>
                        <p>The forest industry is an important source of income and employment in these countries. However, illegal logging and deforestation together can lead to conflicts between logging companies and local communities over access to forest resources.</p>
                        </div>
                    </div>    
                </div>
                <div class = container>
                    <div class = card>
                        <div class = cardImage>
                        <img src="/images/non_timber.webp" alt="People walking through a Guatamalan forest">
                        </div>
                        <div class = content>
                        <h3>Non-timber forest products</h3>
                        <p> Forests provide a range of non-timber forest products (NTFPs) that can be used for food, medicine, and other purposes. The photo shows xate, a palm leaf exported to the United States for flower arrangements, at a workshop in Uaxactún.</p>
                        </div>
                    </div>    
                </div>
                <div class = container>
                    <div class = card>
                        <div class = cardImage>
                        <img src="/images/ecotourism.webp" alt="People walking through a Guatamalan forest">
                        </div>
                        <div class = content>
                        <h3>Ecotoursim</h3>
                        <p>Forests are also an important attraction for ecotourism in these countries. When forests are degraded or destroyed, it can reduce the attractiveness of these areas for tourists, which can have negative impacts on the tourism industry</p>
                        </div>
                    </div>    
                </div>
            </div>
        </section>
        <section id="natural-disasters" style="top: -70vh; left: 5vw; width: 100vw; display:flex; justify-content: center; background: white;">
            <div class="paragraph">
                <img src="images/flood.jpg" style="width: 600px" alt="Flood in Honduras">
                <br><br>
                <h3 style="text-align: center;">Deforestation's indirect link to loss of livelihoods through climate change</h3> <br>
                Deforestation is a significant source of greenhouse gas emissions, which contribute to global warming and climate change, which can in turn disrupt local weather patterns, leading to more frequent and severe natural disasters such as floods and droughts. 
            </div>
        </section>
        <section>
            <div class = "text">
                <h3 style="text-align: center;">Climate change and the agriculture, forestry and fishing industries</h3>
                The agriculture, forestry, and fishing industries in Guatemala, El Salvador, and Honduras provide income and food for millions of people in the region, but they are also highly vulnerable to the impacts of deforestation and climate change. Rising temperatures, changes in precipitation patterns, and more frequent extreme weather events such as floods and droughts are already affecting these industries, leading to crop failures, reduced yields, and damage to infrastructure.
            </div>
        </section>
        <section>
            <div class = "text">
                <h3>Deforestation and Flooding </h3>
                Deforestation reduces the soil's ability to absorb and store water, leading to rapid runoff during heavy rains and increasing the risk of flooding. Trees also play a critical role in intercepting rainfall and reducing the impact of raindrops on the soil, and their removal can exacerbate soil compaction and reduce the water storage capacity in the landscape. 
                The loss of trees also makes hillsides and slopes more vulnerable to erosion and landslides, leading to blockages in rivers and streams that increase the risk of flooding.
            </div>
        </section>
        <section>
            <div class = "text">
                The immediate effect of floods led to the displacement of over a million people in the period of 2008 to 2021 and the effects of floods, including the loss of property, livelihoods, and access to basic services, can be long-lasting. As a result, people may turn to external migration as their last resort for a chance to survive. 
            </div>
        </section>
        <section id="migration">
            <div class = "text">
                Survey data from the World Food Program shows that motivations related to livelihood and food security are among the top 5 of why Central Americans choose to migrate, both externally and internally. 
            </div>
        </section>
        <section>
            <div class = "text">
                As deforestation negatively impacts livelihood and food security, it will likely lead to more migration from the affected regions.
            </div>
        </section>
    </div>
</Scroller>


<style>

    * { 
        margin : 0;
        padding: 0;
        box-sizing : border-box;
    }

    .attribution{
        position: absolute;
        bottom: 15px;
        left: 15px;
        font-size: 0.9em;
    }

    .attribution a, .map-attribution a{
        color: #524eb7;
        text-decoration: none;
    }

    .map-attribution{
        position: absolute;
        bottom: 10px;
        left: 10px;
        font-size: 10px;
        z-index: 100000;
    }

    :global(body){
        padding: 0;
        margin: 0;
        overflow-x: hidden;
        width: 100vw;
    }

    :global(svelte-scroller-foreground){
        pointer-events: none;
    }

    :global(h1, h2, h3, h4, h5, h6){
        font-family: 'Source Serif Pro', serif;
    }

    :global(p, a){
        font-family: Helvetica, Arial;
		font-weight: 200;
    }

    .background {
        width: 100vw;
        height: 100vh;
        position: relative;
    }
    

    .chartBackground{
		/* background: whitesmoke; */
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

    .container {
    position : relative;
    width : 100vw;
    display : flex;
    align-items : center;
    justify-content : center;
    flex-wrap : wrap;
    padding : 30px;  
    }

    .container .card {
    position: relative;
    /* max-width : 30vw; */
    width: 300px;
    height : 175px;  
    background-color : #fff;
    margin : 30px 10px;
    padding : 20px 15px;
    
    display : flex;
    flex-direction : column;
    box-shadow : 0 5px 20px rgba(0,0,0,0.5);
    transition : 0.3s ease-in-out;
    border-radius : 15px;
    }
    .container .card:hover {
    height : 60vh;    
    }


    .container .card .cardImage {
    position : relative;
    width : 260px;
    height : 260px;
    
    top : -40%;
    left: 8px;
    box-shadow : 0 5px 20px rgba(0,0,0,0.2);
    z-index : 1;
    }

    .container .card .cardImage img {
    max-width : 100%;
    border-radius : 15px;
    }

    .container .card .content {
    position : relative;
    top : -140px;
    padding : 10px 15px;
    color : #111;
    text-align : center;
    
    visibility : hidden;
    opacity : 0;
    transition : 0.3s ease-in-out;
        
    }

    .container .card:hover .content {
    margin-top : 30px;
    visibility : visible;
    opacity : 1;
    transition-delay: 0.2s;
    
    }

</style>
