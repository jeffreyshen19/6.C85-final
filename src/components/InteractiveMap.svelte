<script>
    import "leaflet/dist/leaflet.css";
    import {onMount} from "svelte";
    import departments from "../geojson/departments.json";
    import Choropleth from "./Choropleth.svelte";
    import CountryBorders from "./CountryBorders.svelte";
    import CircleLayer from "./CircleLayer.svelte";
    import * as d3 from "d3";

    let L;
    let map;

    const wfpDepartments = new Set(['1184', '901730', 'GT20', '1193', 'GT12', '901742', 'GT13', '901726', 'GT16', '1197', '1185', '901732']);

    onMount(async () => {

        const l = await import('leaflet');
        L = l.default;

        map = L.map('interactive-map', { 
            zoomControl: false,
            attributionControl: false,
            dragging: false,
            doubleClickZoom: false,
            boxZoom: false,
            keyboard: false,
            scrollWheelZoom: false,
            touchZoom: false
        }).setView([15, -89], 6);
    });

    let choropleths = [
        // Neutral
        {
            "domain": [0, 1], 
            "colorScale": d3.scaleSequential(d3.interpolateRgb("#fff", "#000")),
            "f": (d) => 0,
            "formatDomain": (d) => d,
            "showScale": false,
            "scaleLabel": "",
            "title": ""
        }, 
        // Tree Cover
        {
            "domain": [0, 1], 
            "colorScale": d3.scaleSequential(d3.interpolateGreens),
            "f": (d) => d.COVER_2000 / d.TOTAL_SQUA,
            "formatDomain": (d) => (100 * d).toFixed(2) + "%",
            "scaleLabel": "% Tree Cover (2000)",
            "title": "Forest Cover (2000)"
        }, 
        // Forest Loss (10 Year)
        {
            "domain": [0, 0.25], 
            "colorScale": d3.scaleSequential(d3.interpolateReds),
            "f": (d) => d.LOSS_10_YE / d.COVER_2000,
            "formatDomain": (d) => (100 * d).toFixed(2) + "%",
            "scaleLabel": "Forest Cover Loss (2012-2021)",
            "title": "Forest Cover Loss (2012-2021)"
        }, 
        // Forest Loss (5 Year)
        {
            "domain": [0, 0.25], 
            "colorScale": d3.scaleSequential(d3.interpolateReds),
            "f": (d) => d.LOSS_5_YEA / d.COVER_2000,
            "formatDomain": (d) => (100 * d).toFixed(2) + "%",
            "scaleLabel": "Forest Cover Loss (2017-2021)",
            "title": "Forest Cover Loss (2017-2021)"
        }, 
        // Food Insecurity 
        {
            "domain": [0, 0.75], 
            "colorScale": d3.scaleSequential(d3.interpolateRgb("#fff", "#d33800")),
            "f": (d) => d.FOOD_INSECURITY_COUNT/ d.SURVEYED_SIZE,
            "formatDomain": (d) => isNaN(d) ? "No data" : (100 * d).toFixed(2) + "%",
            "scaleLabel": "Food insecurity (%)",
            // "highlight": (d) => wfpDepartments.has(d.department_id),
            "title": "Food Insecurity"
        }, 
    ]
    let choroplethI = 0;

    let circles = [
        {
            "domain": [0, 250],
            "f": (d) => parseInt(d.num_int_migrated) ? 0 : null,
            "title": "",
            "scaleLabel": "",
            "showScale": false
        },
        // {
        //     "domain": [0, 250],
        //     "f": (d) => parseInt(d.num_int_migrated),
        //     "formatDomain": (d) => isNaN(d) ? "No data" : d,
        //     "title": "Number of Internal Migrants", //todo: add clarification that this is from wfp,
        //     "scaleLabel": "Number of Internal Migrants"
        // },
        {
            "domain": [0, 0.75],
            "f": (d) => parseInt(d.num_int_migrated) / parseInt(d.SURVEYED_SIZE),
            "formatDomain": (d) => isNaN(d) ? "No data" : (100 * d).toFixed(2) + "%",
            "title": "Number of Internal Migrants (Per Capita)",
            "scaleLabel": "% of Respondents who Internally Migrated"
        },
        // {
        //     "domain": [0, 250],
        //     "f": (d) => parseInt(d.num_ext_migrated),
        //     "formatDomain": (d) => isNaN(d) ? "No data" : d,
        //     "title": "Number of External Migrants",
        //     "scaleLabel": "Number of External Migrants"
        // },
        {
            "domain": [0, 0.75],
            "f": (d) => parseInt(d.num_ext_migrated)  / parseInt(d.SURVEYED_SIZE),
            "formatDomain": (d) => isNaN(d) ? "No data" : (100 * d).toFixed(2) + "%",
            "title": "Number of External Migrants (Per Capita)",
            "scaleLabel": "% of Respondents who Externally Migrated"
        },
    ]

    let circlesI = 0;

    function renderTooltip(d){
        return `
            <h1>${d.department_name}, ${d.country}</h1>
            ${
                choroplethI > 0 ? 
                `<p>${choropleths[choroplethI].title}: ${choropleths[choroplethI].formatDomain(choropleths[choroplethI].f(d))}</p>`
                : ""
            }
            ${
                circlesI > 0 ? 
                `<p>${circles[circlesI].title}: ${circles[circlesI].formatDomain(circles[circlesI].f(d))}</p>`
                : ""
            }
            
            
        `
    }
</script>

<div id = "wrapper">
    <div id = "title"><h1>Explore the Data:</h1></div>
    <div id = "interactive-map"></div>
    <div id = "dropdowns">

        <div class = "dropdown">
            <label for="choropleths">Potential Migration Causes:</label><br>
            <select name="choropleths" bind:value={choroplethI} >
                {#each choropleths as choropleth, i}
                    <option value={i}>
                        {choropleth.title}
                    </option>
                {/each}
            </select>
        </div>

        <div class = "dropdown">
            <label for="circles">Migration Rates:</label><br>
            <select name="circles" bind:value={circlesI} >
                {#each circles as circle, i}
                    <option value={i}>
                        {circle.title}
                    </option>
                {/each}
            </select>
        </div>
    </div>
    {#if map && L}
        <CountryBorders {L} {map}/>
        <Choropleth 
            visible={true}
            {L} 
            {map} 
            data={departments}
            domain={choropleths[choroplethI].domain}
            colorScale={choropleths[choroplethI].colorScale}
            f={choropleths[choroplethI].f}
            formatDomain={choropleths[choroplethI].formatDomain}
            scaleLabel={choropleths[choroplethI].scaleLabel}
            {renderTooltip}
            showScale={"showScale" in choropleths[choroplethI] ? choropleths[choroplethI].showScale : true}
            highlight={"highlight" in choropleths[choroplethI] ? choropleths[choroplethI].highlight : (d) => true}
        />
        <CircleLayer
            {L} 
            {map} 
            data={departments}
            domain={circles[circlesI].domain}
            f={circles[circlesI].f}
            scaleLabel={circles[circlesI].scaleLabel}
            showScale={"showScale" in circles[circlesI] ? circles[circlesI].showScale : true}
        />

    {/if}
</div>



<style>
    #wrapper{
        position: relative;
    }
    #interactive-map { 
        height: 100vh;
        width: 100vw; 
        /* background: none !important; */
    }  
    #dropdowns{
        position: absolute;
        z-index: 500;
        top: 50px; 
        right: 15px;
    }

    #title{
        position: absolute;
        z-index: 500;
        top: 35px; 
        left: 20px;
    }

    .dropdown{
        display: inline-block;
        margin-left: 10px;
    }

    label{
        display: inline-block;
        font-family: Helvetica, Arial;
		font-weight: 300;
        margin-bottom: 5px;
    }

    select{
        font-family: Helvetica, Arial;
        padding: 5px;
    }
</style>
