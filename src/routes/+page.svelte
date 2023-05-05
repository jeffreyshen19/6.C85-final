<svelte:head>
	<link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:ital,wght@0,400;0,700;1,400&family=Source+Serif+Pro:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
</svelte:head>

<script>
    import Scrollytelling from "../components/Scrollytelling.svelte";
    import InteractiveMap from "../components/InteractiveMap.svelte";
    import Header from "../components/Header.svelte";
</script>

<Header/>
<Scrollytelling/>

<div class = "paragraph">
    <h2>Conclusion</h2>
    <p>As data demonstrates, deforestation in El Salvador, Guatemala and Honduras has led to increased migration rates. Consequences of deforestation, such as loss of livelihood, decreased food security, and more severe flooding and drought, has motivated many Central Americans to start a new life in urban centers or in another country. Millions of people have already migrated due to issues with food security, unemployment and natural disaster, and it is estimated that by 2050, there will be over 10 million Central Americans in urban centers. The status quo presents challenges to urban planners and policy makers. Increased migration could lead to heightened urban design pressure, and loss of workforce and residents in rural regions can further degrade local environments and ecosystems. Therefore, we propose the policy makers and international humanitarian investors to act accordingly:
        Encourage both domestic and international investment on forest preservation and climate change mitigation; 
        Provide funds for climate adaptation for affected population, and help reconstruct the regions damaged by natural disasters; 
        Have increased effort in ensuring employment and food security in regions vulnerable from deforestation; 
        Adjust urban planning policies to reflect demands induced by future increase in urban population.
    </p>
</div>

<InteractiveMap/>