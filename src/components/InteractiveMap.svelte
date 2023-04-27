<script>
    import "leaflet/dist/leaflet.css";
    import {onMount} from "svelte";
    import departments from "../geojson/departments.json";

    export let L;
    export let map;
    export let visible;


    // internal migration 
    // external migration 

    
    // food insecurity 
    // deforestation 
    // initial forest cover 


    let choroplethLayer;
    
    
    let osm;

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

        choroplethLayer = L.geoJson(departments, {
            style: function style(feature) {
                return {
                    fillColor: "red",
                    weight: 1.5,
                    color: '#fff',
                };
            },
            // onEachFeature: onEachFeature
        }).addTo(map);

        map.fitBounds(choroplethLayer.getBounds());
    });
</script>

<div id = "interactive-map"></div>

<style>
    #interactive-map { 
        height: 100vh;
        width: 100vw; 
        background: none !important;
    }  
</style>
