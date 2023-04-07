<script>
    export let position;
    export let hidden = false;
</script>

{#if !hidden}
    <div class = "tooltip" style:left="{position.x + 10}px" style:top="{position.y + 10}px">
        <slot></slot>
    </div>
{/if}

<style>
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

    :global(.tooltip h1, p){
        margin: 0;
    }

    :global(.tooltip h1){
        font-size: 1em;
    }

    :global(.tooltip p){
        font-size: 0.75em;
    }
</style>