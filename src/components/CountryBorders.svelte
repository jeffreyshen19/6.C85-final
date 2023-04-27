<script>
    import countries from "../geojson/countries.json";

    export let L;
    export let map;

    let layer;

    $: if(L && map){
        map.createPane('borderPane');
        map.getPane('borderPane').style.zIndex = 401;
        layer = L.geoJson(countries, {
            style: function style(feature) {
                return {
                    fillOpacity: 0,
                    weight: 3,
                    color: '#bbb'
                };
            },
            pane: 'borderPane',
            interactive: false
        }).addTo(map);
    }

</script>
