<svelte:head>
	<link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:ital,wght@0,400;0,700;1,400&family=Source+Serif+Pro:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
</svelte:head>

<script>
    import Choropleth from "../components/Choropleth.svelte";
    import Basemap from "../components/Basemap.svelte";
    import departments from "../geojson/departments.json";
    import * as d3 from "d3";

    export let L; 
    export let map; 
</script>

<Basemap bind:L={L} bind:map={map}/>
<Choropleth 
    {L} 
    {map} 
    data={departments} 
    colorScale={d3.scaleSequential(d3.interpolateGreens)}
    f={(d) => d.COVER_2000 / d.TOTAL_SQUA}
    formatDomain={(d) => (100 * d).toFixed(2)}
    scaleLabel="% Tree Cover (2000)"
    renderTooltip = {(d) => `<h1>${d.department_name}, ${d.country}</h1><p>Tree Cover (2000): ${(d.COVER_2000 / d.TOTAL_SQUA * 100).toFixed(2)}%</p>`}
/>

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
</style>
