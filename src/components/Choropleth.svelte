<script>
    import "leaflet/dist/leaflet.css";
    import departments from "../geojson/departments.json";
    import {onMount} from "svelte";
    import * as d3 from "d3";

    let L;
    let map;
    let departmentsLayer;

    let tooltipPosition; 
    let hovered;

    function f(d){
        return d.COVER_2000 / d.TOTAL_SQUA;
    }

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

    const colors = d3.scaleSequential(d3.interpolateGreens).domain(getDomain(departments.features));

    function getColor(d){
        return colors(f(d));
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
        departmentsLayer.resetStyle(e.target);
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

    onMount(async () => {

        const l = await import('leaflet');
        L = l.default;

        map = L.map('map', { 
            zoomControl: false,
            attributionControl: false,
            dragging: false,
            doubleClickZoom: false,
            boxZoom: false,
            keyboard: false,
            scrollWheelZoom: false,
            touchZoom: false
        }).setView([15, -89], 6);

        var osm = L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
            subdomains: 'abcd',
            maxZoom: 20
        }).addTo( map );

        departmentsLayer = L.geoJson(departments, {
            style: function style(feature) {
                return {
                    fillColor: getColor(feature.properties),
                    weight: 1,
                    opacity: 1,
                    color: 'white',
                    fillOpacity: 0.9,
                    transition: "2s all"
                };
            },
            onEachFeature: onEachFeature
        }).addTo(map);

        map.fitBounds(departmentsLayer.getBounds());

    });
</script>

<div id = "map"></div>
{#if hovered}
    <div class = "tooltip" style:left="{tooltipPosition.x + 10}px" style:top="{tooltipPosition.y + 10}px">
        <h1>{hovered.department_name}, {hovered.country}</h1>
        <p>Tree Cover (2000): {(f(hovered) * 100).toFixed(2)}%</p>
    </div>
{/if}

<style>
    #map { 
        height: 100vh;
        width: 100vw; 
    }

    .tooltip{
        background: white;
        border-radius: 5px;
        position: absolute;
        z-index: 1000;
        -webkit-box-shadow: 10px 10px 45px -6px rgba(75,75,75,0.6);
        -moz-box-shadow: 10px 10px 45px -6px rgba(75,75,75,0.6);
        box-shadow: 10px 10px 45px -6px rgba(75,75,75,0.6);
        padding: 0.75em 1.25em;
    }

    .tooltip h1, p{
        margin: 0;
    }

    .tooltip h1{
        font-size: 1em;
    }

    .tooltip p{
        font-size: 0.75em;
    }
</style>