<script>
    export let index;
    export let progress; 
    export let offset;
    let background;
    $: background = (index <= 1 || (index >= 8 && index < 13) || index >= 16) && progress < 1.1;
</script>

<nav
    style:backdrop-filter={!background ? "blur(1px)" : "none"}
    style:background-color={background ? "white" : "rgba(0,0,0,0)"}
    style:opacity={progress < -0.09 ? 0 : 1}
>
    <a style:font-weight={progress <= 0 ? "bold" : 200} href = "#intro">Intro</a>    
    <a style:font-weight={progress > 0 && index < 6 ? "bold" : 200} href = "#deforestation">Deforestation</a>
    <a style:font-weight={index >= 6 && index < 13 ? "bold" : 200} href = "#food-insecurity">Food Insecurity</a>
    <a style:font-weight={index >= 13 && index < 15 ? "bold" : 200} href = "#livelihood">Livelihood</a>
    <a style:font-weight={index >= 15 && index < 19 ? "bold" : 200} href = "#natural-disasters">Natural Disasters</a>
    <a style:font-weight={index >= 19 && progress < 1 ? "bold" : 200} href = "#migration">Migration</a>
    <a style:font-weight={progress >= 1 && progress < 1.1? "bold" : 200} href = "#conclusion">Conclusion</a>
    <a style:font-weight={progress >= 1.1? "bold" : 200} href = "#interactive-map">Interactive Map</a>
</nav>

<style>
    nav{
        position: fixed;
        top: 0;
        right: 0;
        z-index: 50000;
        padding: 15px 20px;
        transition: 0.2s all;
        box-sizing: border-box;
        width: 100%;
        text-align: left;
    }

    nav a{
        margin-right: 10px;
        text-decoration: none;
        color: black;
        transition: 0.2s all;
    }

</style>