<script>
	import People from "./icons/People.svelte";
	import Tree from "./icons/Tree.svelte";
    
    export let item;

	const people = item.type == "people";

	const agri_people = item.country == "Guatemala" ? item.index < 29 : item.country == "Honduras" ? item.index < 25 : item.country == "El Salvador" ? item.index < 15.2 : false;

	const tree = item.country == "Guatemala" ? item.index < 70 : item.country == "Honduras" ? item.index < 60 : item.country == "El Salvador" ? item.index < 48 : false;

	const before = item.year == 2000;

	const left = item.country == "Guatemala" ? item.index < 49 & item.index < 70 : item.country == "Honduras" ? item.index < 44 & item.index < 60 : item.country == "El Salvador" ? item.index < 40 & item.index < 48 : false;

	const after = item.country == "Guatemala" ? item.index >= 49 & item.index < 70 : item.country == "Honduras" ? item.index >= 44 & item.index < 60 : item.country == "El Salvador" ? item.index >= 40 & item.index < 48 : false;


</script>	

<style>
	.item {
		width:4vh;
		height: 4vh;
		border-radius:10%;
	}
</style>

{#if people & agri_people}
	<div class=item style={`width: 3vh; height: 3vh; filter: invert(33%) sepia(44%) saturate(462%) hue-rotate(80deg) brightness(92%) contrast(86%);`}>
		<People />
	</div>
{:else if people & !agri_people}
	<div class=item style={`width: 3vh; height: 3vh;  filter: grayscale(100%); opacity: 0.2;`}>
		<People />
	</div>
{:else if tree & before}
    <div class=item style={`filter: invert(33%) sepia(44%) saturate(462%) hue-rotate(80deg) brightness(92%) contrast(86%);`}>
        <Tree />
    </div>
{:else if !before & after}
	<div class=item style={`filter: grayscale(100%); opacity: 0.2;`}>
		<Tree />
	</div>
{:else if !before & left}
	<div class=item style={`filter: invert(33%) sepia(44%) saturate(462%) hue-rotate(80deg) brightness(92%) contrast(86%);`}>
		<Tree />
	</div>
{:else}
    <div class=item style={`background-color:#F2E8CF`} />
{/if}

