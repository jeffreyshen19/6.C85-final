<svelte:head>
	<link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:ital,wght@0,400;0,700;1,400&family=Source+Serif+Pro:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
</svelte:head>

<style>
    :global(body){
        padding: 0;
        margin: 0;
    }

    :global(h1, h2, h3, h4, h5, h6){
        font-family: 'Source Serif Pro', serif;
    }

    :global(p){
        font-family: 'IBM Plex Sans', sans-serif;
    }
</style>

<script>
    import Choropleth from "../components/Choropleth.svelte";
</script>

<Choropleth/>
