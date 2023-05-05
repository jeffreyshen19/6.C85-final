<script>
    export let index;
    export let progress; 
    export let offset;
    let background;
    $: background = index <= 1 || (index >= 8 && index < 13) || index >= 16;
</script>

<nav
    style:backdrop-filter={!background ? "blur(1px)" : "none"}
    style:background-color={background ? "white" : "rgba(0,0,0,0)"}
    style:opacity={progress < -0.09 || progress > 1.1 ? 0 : 1}
>
    <a href = "#intro">Intro</a>    
    <a href = "#deforestation">Deforestation</a>
    <a href = "#food-insecurity">Food Insecurity</a>
    <a href = "#livelihood">Livelihood</a>
    <a href = "#natural-disasters">Natural Disasters</a>
    <a href = "#migration">Migration</a>
    <a href = "#conclusion">Conclusion</a>
</nav>

<style>
    nav{
        width: 100vw;
        position: fixed;
        top: 0;
        left: 0;
        z-index: 50000;
        padding: 15px 20px;
        transition: 0.2s all;
        box-sizing: border-box;
    }

    nav a{
        margin-right: 10px;
        text-decoration: none;
        color: black;
    }

</style>