<script>
    import Tooltip from "../components/Tooltip.svelte";
    import { onDestroy } from "svelte";

    export let L;
    export let map;
    export let data;
    export let formatDomain;
    export let scaleLabel;
    export let colorScale;
    let domain; 
    $: if(data) domain = getDomain(data.features);
    $: if(colorScale && data) colorScale = colorScale.domain(domain);

    export let f; // a method returning the attribute to visualize on the choropleth
    export let renderTooltip; // a method returning the html of the tooltip 
    export let visible;

    let choroplethLayer;
    let tooltipPosition; 
    let hovered;
    

    function getDomain(data){
        let min, max;
        data.forEach((d) => {
            let t = f(d.properties);
            if(isNaN(min)) min = t;
            else min = Math.min(min, t);

            if(isNaN(max)) max = t;
            else max = Math.max(max, t);
        });

        return [min, max];
    }

    // Handle tooltip 
    $: if(L && map){
        choroplethLayer = L.geoJson(data, {
            style: function style(feature) {
                return {
                    fillColor: colorScale(f(feature.properties)),
                    weight: 1,
                    opacity: 0,
                    color: 'white',
                    fillOpacity: 0
                };
            },
            onEachFeature: onEachFeature
        }).addTo(map);

        // map.fitBounds(choroplethLayer.getBounds());
    }

    $: if(choroplethLayer) {
        if(visible){
            choroplethLayer.setStyle({
                opacity: 1,
                fillOpacity: 0.9
            })
        } 
        else {
            choroplethLayer.setStyle({
                opacity: 0,
                fillOpacity: 0
            })
        }
    }

    function highlightFeature(e) {
        var layer = e.target;

        hovered = e.target.feature.properties;
        moveTooltip(e);

        layer.setStyle({
            weight: 5,
        });

        layer.bringToFront();
    }

    function moveTooltip(e){
        tooltipPosition = e.containerPoint;
    }

    function resetHighlight(e) {
        choroplethLayer.resetStyle(e.target);
        tooltipPosition = null;
        hovered = null;
    }

    function onEachFeature(feature, layer) {
        layer.on({
            mouseover: highlightFeature,
            mouseout: resetHighlight,
            mousemove: moveTooltip
        });
    }

</script>

<Tooltip hidden={hovered==null} position={tooltipPosition}>
    {@html renderTooltip(hovered)}
</Tooltip>

{#if domain && colorScale}
    <div class = "scale">
        <p><strong style:font-size="14px">{scaleLabel}</strong></p>
        <div 
            style:background-image="linear-gradient(to right, {colorScale(domain[0])}, {colorScale((domain[1] - domain[0]) * 0.25)}, {colorScale((domain[1] - domain[0]) * 0.5)}, {colorScale((domain[1] - domain[0]) * 0.75)},{colorScale(domain[1])})"
        ></div>
        <p style:font-size="10px">
            <span style:float="left">{formatDomain(domain[0])}%</span>
            <span style:float="right">{formatDomain(domain[1])}%</span>
        </p>
    </div>
{/if}

<style>
    :global(.leaflet-interactive){
        transition: fill-opacity 1s, stroke-opacity 1s;
    }

    .scale{
        position: absolute;
        z-index: 1000;
        bottom: 10px;
        left: 10px;
    }

    .scale div{
        width: max(160px, 100%);
        height: 20px;
        margin: 4px 0;
        opacity: 0.9;
    }
    
    .scale p{
        width: max(160px, 100%);
    }
</style>
