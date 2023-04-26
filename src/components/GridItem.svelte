<script>
	import Tree from "./icons/Tree.svelte";
    
    export let item;

	const tree = item.country == "Guatemala" ? item.index < 70 : item.country == "Honduras" ? item.index < 60 : item.country == "El Salvador" ? item.index < 48 : false;

</script>	

<style>
	.item {
		width:5vh;
		height:5vh;
		border-radius:10%;
	}
</style>

{#if tree}
    <div class=item style={`filter: invert(33%) sepia(44%) saturate(462%) hue-rotate(80deg) brightness(92%) contrast(86%);`}>
        <Tree />
    </div>
{:else}
    <div class=item style={`background-color:#F2E8CF`} />
{/if}

