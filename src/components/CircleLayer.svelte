<script>
    import * as d3 from "d3";

    export let L;
    export let map;
    export let data;
    export let scaleLabel;
    export let showScale = true;
    export let domain;

    let sizeScale;
    $: if(domain) sizeScale = d3.scaleLinear().range([0, 100000]).domain(domain);

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
                    circles[d.properties.department_id].setRadius(sizeScale(f(d.properties))).setStyle({
                        opacity: f(d.properties) ? 1 : 0,
                        fillOpacity: f(d.properties) ? 0.4 : 0,
                    })
                };
            })
        }
    }

    // $: {
    //     if(choroplethLayer) choroplethLayer.setStyle((feature) => {
    //         return {
    //             fillColor: colorScale(f(feature.properties)),
    //             opacity: visible && highlight(feature.properties) ? 1 : 0,
    //             fillOpacity: visible ? (highlight(feature.properties) ? 0.9 : 0.02) : 0,
    //         }
    //     })
    // }

</script>

<!-- {#if sizeScale && showScale}
    <div class = "scale">
        <p><strong style:font-size="14px">{scaleLabel}</strong></p>

        {#each [0, 1, 2, 3] as i}
            <div
                style:border-radius="100%"
                style:background-color="rgba(40,67,135,0.4)"
                style:border="1px solid #284387"
                style:width="{sizeScale((domain[1] - domain[0]) * i / 3 + domain[0]) / 2}px"
                style:height="{sizeScale((domain[1] - domain[0]) * i / 3 + domain[0]) / 2}px"
            ></div>
        {/each}
        <p style:font-size="10px">
            <span style:float="left">{formatDomain(domain[0])}%</span>
            <span style:float="right">{formatDomain(domain[1])}%</span>
        </p>
    </div>
{/if} -->

<style>

    .scale{
        position: absolute;
        z-index: 1000;
        bottom: 10px;
        right: 10px;
    }

    

</style>
