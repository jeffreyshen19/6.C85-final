<script>
    import * as d3 from "d3";
    import Tooltip from "../components/Tooltip.svelte";

    export let title;
    export let caption = "";
	export let data;
    export let highlighted = [];
    $: highlightedIndexes = new Set(highlighted);

    export let xAxisLabel = "";
    export let xTicks;
    export let formatXTick = (d) => d;
    export let visible;

	const padding = { top: 20, right: 30, bottom: 50, left: 270 };

	let width = 700;
	let height = 400;

	const yScale = d3.scaleLinear()
		.domain([0, data.length])
		.range([padding.top, height - padding.bottom]);

	const xScale = d3.scaleLinear()
		.domain([0, Math.max.apply(null, xTicks)])
		.range([0, width - padding.right - padding.left]);

	const innerHeight = height - (padding.top + padding.bottom);
	const barHeight = innerHeight / data.length - 4;

    let tooltipPosition = {
        "x": 0,
        "y": 0
    }

    let hovered = null;

    function renderTooltip(d){
        return `
            <p><strong>${d[0]}</strong>: ${d[1]} respondents</p>
        `;
    }

    function mousemove(e){
        tooltipPosition.x = e.clientX;
        tooltipPosition.y = e.clientY;
    }
</script>

<div class = "wrapper" style:opacity={visible ? 1 : 0} style:pointer-events={visible ? "all" : "none"}>
    <h2>{title}</h2>
    <div class="chart" bind:clientWidth={width} bind:clientHeight={height}>
        <svg>
            <g class="axis y-axis">
                {#each data as point, i}
                    <g class="tick" transform="translate({padding.left}, {yScale(i)})">
                        <text style:text-anchor="end">{point[0]}</text>
                    </g>
                {/each}

            </g>

            <!-- x axis -->
            <g class="axis x-axis">
                {#each xTicks as tick}
                    <g class="tick tick-{tick}" transform="translate({padding.left + xScale(tick) + 10},{height - padding.bottom + 12})">
                        <line y1="-5%" y2="-100%"></line>
                        <text y="-4">{formatXTick(tick)}</text>
                    </g>
                {/each}

                <g class = "x-axis-label" transform="translate({padding.left + (width - padding.left - padding.right)/2}, {height - padding.bottom + 30})">
                    <text style:text-anchor="middle" dominant-baseline="middle">{xAxisLabel}</text>
                </g>

                <!-- {#each data as point, i}
                    <g class="tick" transform="translate({padding.left + xScale(i) + barWidth / 2},{height - padding.bottom + 12})">
                        <text transform="rotate(-50)" dx=".4em" dy="0.3em"  style:text-anchor="end">{point[0]}</text>
                    </g>
                {/each} -->
            </g>
    
            <g class='bars'>
                {#each data as point, i}
                    <rect
                        y="{yScale(i) - (10 + barHeight) / 2}"
                        x="{padding.left + 10}"
                        height="{barHeight}"
                        width="{xScale(point[1])}"
                        stroke={highlightedIndexes.has(i) ? "#ede99d" : "none"}
                        on:mouseenter={() => {hovered = point;}} 
                        on:mousemove={mousemove} 
                        on:mouseleave={() => {hovered = null;}} 
                    ></rect>
                {/each}
            </g>
        </svg>
    </div>
    <p class = "caption">{caption}</p>
</div>

<Tooltip hidden={hovered == null} position={tooltipPosition}>
    {@html renderTooltip(hovered)}
</Tooltip>

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
		max-width: 700px;
        transition: opacity 0.4s;
        z-index: 1000;
    }

	svg {
		position: relative;
		width: 100%;
		height: 400px;
	}

	.tick, .x-axis-label     {
		font-family: Helvetica, Arial;
		font-size: 12px;
		font-weight: 200;
        text-align: top;
	}

    .caption{
        font-family: Helvetica, Arial;
		font-weight: 200;
        text-align: center;
        font-size: 14px;
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
		fill: #284387;
        stroke-width: 3;
		opacity: 0.65;
	}
</style>
