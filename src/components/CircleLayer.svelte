<script>
    import * as d3 from "d3";

    export let L;
    export let map;
    export let data;
    export let scaleLabel;
    export let showScale = true;
    export let domain;

    let sizeScale;
    $: if(domain) sizeScale = d3.scaleLinear().range([0, 25]).domain(domain);

    let metersPerPixel;
    $: metersPerPixel = 40075016.686 * Math.abs(Math.cos(map.getCenter().lat * Math.PI/180)) / Math.pow(2, map.getZoom()+8);

    export let f; // a method returning the attribute to visualize on the choropleth
    export let visible;

    let circles = {};

    $: if(L && map && !Object.keys(circles).length){
        map.createPane('circlePane');
        map.getPane('circlePane').style.zIndex = 402;
        data.features.forEach((d) => {
            let x = d.properties.x, y = d.properties.y;
            if(f(d.properties)) {
                let circle = L.circle([y, x], {
                    color: "#284387",
                    pane: 'circlePane',
                    interactive: false
                }).addTo(map)
                circles[d.properties.department_id] = circle;
             }
        })
    }

    $: {
        if(Object.keys(circles).length){
            data.features.forEach((d) => {
                if(d.properties.department_id in circles) {
                    circles[d.properties.department_id].setRadius(sizeScale(f(d.properties)) * metersPerPixel).setStyle({
                        opacity: f(d.properties) ? 1 : 0,
                        fillOpacity: f(d.properties) ? 0.4 : 0,
                    })
                };
            })
        }
    }


</script>

{#if sizeScale && showScale}
    <div class = "scale">
        <p><strong style:font-size="14px">{scaleLabel}</strong></p>

        {#each [1, 2, 3] as i}
            <div
                style:border-radius="100%"
                style:background-color="rgba(40,67,135,0.4)"
                style:border="2px solid #284387"
                style:margin="{10 + 2 * sizeScale(domain[1]) - 2 * sizeScale((domain[1] - domain[0]) * i / 3 + domain[0])}px 20px"
                style:width="{sizeScale((domain[1] - domain[0]) * i / 3 + domain[0]) * 4}px"
                style:height="{sizeScale((domain[1] - domain[0]) * i / 3+ domain[0]) * 4}px"
                style:position="relative"
                style:display="inline-block"
            >
                <p
                    style:font-size="10px"
                    style:position="absolute"
                    style:top="{sizeScale((domain[1] - domain[0]) * i / 3 + domain[0]) * 4 + 5}px"
                    style:width="100%"
                    style:text-align="center"
                >{(((domain[1] - domain[0]) * i / 3 + domain[0]) * 100)}%</p>
            </div>
        {/each}
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
        right: 10px;
        transition: 1s all;
    }
    
    .scale div{
        margin-left: 0 !important;
    }

    .scale p{
        width: max(160px, 100%);
    }
</style>
