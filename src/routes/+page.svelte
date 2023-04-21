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
    import departments from "../geojson/departments.json";
    import fi_departments from "../geojson/food_insecurity.json";
    import Scroller from "@sveltejs/svelte-scroller";
    import * as d3 from "d3";

    let L; 
    let map; 

    let count, index, offset, progress;
    let width, height;
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


        <Basemap 
            bind:L={L} bind:map={map}
            visible={index <= 5}
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
            {L} 
            {map} 
            data={departments} 
            colorScale={d3.scaleSequential(d3.interpolateGreens)}
            f={(d) => d.COVER_2000 / d.TOTAL_SQUA}
            formatDomain={(d) => (100 * d).toFixed(2)}
            scaleLabel="% Tree Cover (2000)"
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>Tree Cover (2000): ${(d.COVER_2000 / d.TOTAL_SQUA * 100).toFixed(2)}%</p>`}
        />
        <Raster
            {L}
            {map}
            url="/raster/loss.png"
            visible={index == 3}
            bounds={[[17.8292499999999983, -92.25], [12.971, -83.14575]]}
        />
        <Choropleth 
            visible={index == 4}
            {L} 
            {map} 
            data={departments} 
            colorScale={d3.scaleSequential(d3.interpolateReds)}
            f={(d) => d.LOSS_10_YE / d.TOTAL_SQUA}
            formatDomain={(d) => (100 * d).toFixed(2)}
            scaleLabel="Forest Cover Loss (2012-2021)"
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>Forest Cover Loss (2012-2021): ${(d.LOSS_10_YE / d.TOTAL_SQUA * 100).toFixed(2)}%</p>`}
        />
        <!-- <Choropleth 
            visible={index == 5}
            {L} 
            {map} 
            data={fi_departments} 
            colorScale={d3.scaleSequential(d3.interpolateYlOrRd)}
            f={(d) => d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE}
            formatDomain={(d) => (100 * d).toFixed(2)}
            scaleLabel="Food insecurity (%)"
            renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>% of population facing food insecurity: ${(d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE * 100).toFixed(2)}%</p>`}
        /> -->
        <BarChart 
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
        />

    </div>
  
    <div class="foreground" slot="foreground">
        <section>This is the first section.</section>
        <section>This is the second section.</section>
        <section>This is the third section.</section>
        <section>This is the fourth section.</section>
        <section>This is the fifth section.</section>
        <section>This is the sixth section.</section>
        <section>This is the seventh section.</section>
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
  
    .foreground {
      width: 50%;
      /* margin: 0 auto; */
      height: auto;
      position: relative;
      outline: red solid 3px;
    }
  
    .progress-bars {
      position: absolute;
      background: rgba(170, 51, 120, 0.2) /*  40% opaque */;
      visibility: visible;
      z-index: 1000;
    }
  
    section {
      height: 80vh;
      background-color: rgba(0, 0, 0, 0.2); /* 20% opaque */
      /* color: white; */
      outline: magenta solid 3px;
      text-align: center;
      max-width: 750px; /* adjust at will */
      color: black;
      padding: 1em;
      margin: 0 0 2em 0;
    }
</style>
