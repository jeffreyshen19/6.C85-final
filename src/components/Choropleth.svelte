<script>
    import Tooltip from "../components/Tooltip.svelte";

    export let L;
    export let map;
    export let data;
    export let colorScale;
    $: if(colorScale) colorScale = colorScale.domain(getDomain(data.features));

    export let f; // a method returning the attribute to visualize on the choropleth
    export let renderTooltip; // a method returning the html of the tooltip 

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
                    opacity: 1,
                    color: 'white',
                    fillOpacity: 0.9,
                    transition: "2s all"
                };
            },
            onEachFeature: onEachFeature
        }).addTo(map);

        map.fitBounds(choroplethLayer.getBounds());
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
