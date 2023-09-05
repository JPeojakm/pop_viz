<script>
  import data from "$data/data.js";
  import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";
  import { scaleLinear, scaleBand, scaleOrdinal, scaleSqrt } from "d3-scale";
  import { extent } from "d3-array";

  let width = 300, 
      height = 420;

  const RADIUS = 5;
  const margin = { top: 0, right: 20, bottom: 25, left: 20 };

  $: innerWidth = width - margin.right - margin.left;
  let innerHeight = height - margin.top - margin.bottom;

  import { mean, rollups } from "d3-array";

  const location = rollups(
    data,
    v => mean(v, d => d.change),
    d => d.location
  ) 
    .sort((a, b) => a[1] - b[1]) 
    .map(d => d[0]); 

    const colorRange = ["#00AEFF", "#FF028C", "#FFC402", "#CCCCCC"];
    const colorScale = scaleOrdinal()
    .domain(location) 
    .range(colorRange);

  let simulation = forceSimulation(data);
    $: {
      simulation
      .force("x", forceX().x(d => xScale(d.change)).strength(1.3))
      .force("y", forceY()
      .y(d => groupByLocation ? yScale(d.location) : innerHeight / 2)
      .strength(0.2))
      .force("collide", forceCollide().radius(d => radiusScale(d.change)+ 3)) //prevent overlap
      .alpha(0.3) 
      .alphaDecay(0.02)
      .restart();
  }

  $: xScale = scaleLinear()
    .domain(extent(data, d => d.change))
    .range([0, innerWidth]);

  let yScale = scaleBand()
    .domain(data.map(d => d.location))
    .range([innerHeight, 0])
    .paddingOuter(0.5);

  $: radiusScale = scaleSqrt()
    .domain(extent(data, d => d.pop2022)) // The same domain passed to xScale
    .range(width < 568 ? [2,6] : [4, 9]);

  let nodes = [];
  simulation.on("tick", () => {
    nodes = simulation.nodes();
  });

  $: console.log(nodes);

  import AxisX from "$components/AxisX.svelte";
  import AxisY from "$components/AxisY.svelte";
  import Legend from "$components/Legend.svelte";
  import Tooltip from "$components/Tooltip.svelte";

  let hovered;
    $: console.log(hovered) // To keep track of what is being hovered

  let hoveredLocation;

  let groupByLocation = false;

</script>

<h1>population change</h1>
<Legend {colorScale} bind:hoveredLocation />
<div class='chart-container' bind:clientWidth={width}
on:click={() => {
  groupByLocation = !groupByLocation;
  hovered = null;
}}>
  <svg {width} {height}>
    <g class="inner-chart" transform="translate({margin.left}, {margin.top})">
      <AxisX {xScale} height={innerHeight} width={innerWidth}/>
      <AxisY {yScale} {groupByLocation} />
      {#each nodes as node}
        <circle  cx={node.x} cy={node.y} r={radiusScale(node.pop2022)} 
        fill={colorScale(node.location)} 
        stroke={hovered || hoveredLocation
          ? hovered === node || hoveredLocation === node.location
          ? "black": "transparent": "#00000090"}
          title={node.county}
          opacity={hovered || hoveredLocation
                   ? hovered === node || hoveredLocation === node.location
                      ? 1: 0.3: 1}
        on:mouseover={() => hovered = node}
        on:focus={() => hovered = node}
        tabindex="0" 
        on:click={(e) => {
          e.stopPropagation();
        }}
        />
      {/each}
    </g>
  </svg>

  {#if hovered}
    <Tooltip data={hovered} {colorScale}{width} />
{/if}
</div>


<style>
  :global(.tick text, .axis-title) {
    font-size: 14px; 
    font-weight: 400; /* How bold our text is */
    fill: hsla(212, 10%, 53%, 1); 
    user-select: none; /* Prevents text from being selected */
}

 :global(.axis-title){
   font-size: 16px;
 }

 h1{
   font-size: 22px;
   margin: 0 0 6px 0;
   font-weight: 600;
   text-align: center;
 }

 circle {
    transition: stroke 300ms ease, opacity 300ms ease;
    cursor: pointer;
}
</style>