<script>
    import Tooltip from "../components/Tooltip.svelte";

    export let L;
    export let map;
    export let url;
    export let bounds;
    export let visible;
    let imageLayer;

    $: if(L && map){
        imageLayer = L.imageOverlay(url, bounds).addTo(map);
    }

    $: if(imageLayer) {
        if(visible){
            imageLayer.setOpacity(1);
        } 
        else {
            imageLayer.setOpacity(0);
        }
    }
</script>
<!-- {#if domain && colorScale}
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
-->
<style>
    :global(.leaflet-image-layer){
        transition: opacity 1s;
    }
</style> 
