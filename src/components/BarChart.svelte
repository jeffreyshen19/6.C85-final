<script>
    import * as d3 from "d3";

    export let title = "External migration due to the direct impact of a natural hazard, by department";
	export const data = [
        ["Alta Verapaz, GT", 0.05660377358490566],
        ["Yoro, HND", 0.03015075376884422],
        ["Cortes, HND", 0.01694915254237288],
        ["San Marcos, GT", 0.008771929824561403],
        ["Ahuachapan, SLV", 0.0],
        ["Cabañas, SLV", 0.0],
        ["San Salvador, SLV", 0.0],
        ["Usulutan, SLV", 0.0],
        ["Choluteca, HND", 0.0],
        ["Francisco Morazan, HND", 0.0],
        ["Huehuetenango, GT", 0.0],
        ["Chiquimula, GT", 0.0],
    ];
    export let highlighted = new Set([0, 1]);

	// const xTicks = [1990, 1995, 2000, 2005, 2010, 2015];
	const yTicks = [0, 0.02, 0.04, 0.06, 0.08, 0.1];
	const padding = { top: 20, right: 15, bottom: 175, left: 40 };

	let width = 500;
	let height = 350;

	$: xScale = d3.scaleLinear()
		.domain([0, data.length])
		.range([padding.left, width - padding.right]);

	$: yScale = d3.scaleLinear()
		.domain([0, Math.max.apply(null, yTicks)])
		.range([height - padding.bottom, padding.top]);

	$: innerWidth = width - (padding.left + padding.right);
	$: barWidth = innerWidth / data.length;
</script>

<div class = "wrapper">
    <h2>{title}</h2>
    <div class="chart" bind:clientWidth={width} bind:clientHeight={height}>
        <svg>
            <!-- y axis -->
            <g class="axis y-axis">
                {#each yTicks as tick}
                    <g class="tick tick-{tick}" transform="translate(0, {yScale(tick)})">
                        <line x2="100%"></line>
                        <text y="-4">{tick * 100}% {tick == 0.06 ? " of respondents in that department" : ""}</text>
                    </g>
                {/each}
            </g>
    
            <!-- x axis -->
            <g class="axis x-axis">
                {#each data as point, i}
                    <g class="tick" transform="translate({xScale(i) + barWidth / 2},{height - padding.bottom + 12})">
                        <text transform="rotate(-50)" dx=".4em" dy="0.3em"  style:text-anchor="end">{point[0]}</text>
                    </g>
                {/each}
            </g>
    
            <g class='bars'>
                {#each data as point, i}
                    <rect
                        x="{xScale(i) + 2}"
                        y="{yScale(point[1])}"
                        width="{barWidth - 4}"
                        height="{yScale(0) - yScale(point[1])}"
                        stroke={highlighted.has(i) ? "#ede99d" : "none"} 
                    ></rect>
                {/each}
            </g>
        </svg>
    </div>
</div>

<style>
	h2 {
		text-align: center;
	}

    .wrapper{
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 100%;
		max-width: 500px;
    }

	svg {
		position: relative;
		width: 100%;
		height: 350px;
	}

	.tick {
		font-family: Helvetica, Arial;
		font-size: .725em;
		font-weight: 200;
	}

	.tick line {
		stroke: #e2e2e2;
		stroke-dasharray: 2;
	}

	.tick text {
		fill: #ccc;
		text-anchor: start;
	}

	.tick.tick-0 line {
		stroke-dasharray: 0;
	}

	.x-axis .tick text {
		text-anchor: middle;
	}

	.bars rect {
		fill: #a11;
        stroke-width: 3;
		opacity: 0.65;
	}
</style>
