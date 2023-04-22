<script>
    import "leaflet/dist/leaflet.css";
    import {onMount} from "svelte";

    export let L;
    export let map;
    export let visible;
    
    let osm;

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

        osm = L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
            subdomains: 'abcd',
            maxZoom: 20
        }).addTo(map);
    });
</script>

<div id = "map" style:opacity={visible ? 1 : 0}></div>

<style>
    #map { 
        height: 100vh;
        width: 100vw; 
        transition: opacity 0.4s;
    }
    
</style>
