<script>
    import "leaflet/dist/leaflet.css";
    import departments from "../geojson/departments.json";
    import {onMount} from "svelte";
    import * as d3 from "d3";

    let L;
    let map;

    function f(d){
        return d.COVER_2000 / d.TOTAL_SQUA;
    }

    function getDomain(data){
        let min, max;
        data.forEach((d) => {
            let t = f(d.properties);
            console.log(t)
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

        let departmentsLayer = L.geoJson(departments, {
            style: function style(feature) {
                return {
                    fillColor: getColor(feature.properties),
                    weight: 1,
                    opacity: 1,
                    color: 'white',
                    fillOpacity: 0.9
                };
            }
        }).addTo(map);

        map.fitBounds(departmentsLayer.getBounds());

    });
</script>

<div id = "map"></div>

<style>
    #map { 
        height: 100vh;
        width: 100vw; 
    }
</style>