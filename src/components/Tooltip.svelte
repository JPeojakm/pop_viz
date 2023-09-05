<script>
    export let data;
    
    export function formatData(data) {
    const formattedPopulation = "2022 population: "+ (data.pop2022 / 1000).toFixed(0) + "k";
    const formattedChange = data.change.toFixed(2) + "%";
    return [data.county, formattedPopulation, formattedChange];
  };

    export let colorScale;

    let color;
  $: {
    color = colorScale(data.location);
  }

  import{fly, fade} from "svelte/transition";

  export let width;
  let tooltipwidth;

  const xNudge = 5;
  const yNudge = 5; 

  $: xPosition =
    data.x + tooltipwidth + xNudge > width
      ? data.x - tooltipwidth - xNudge
      : data.x + xNudge;
  $: yPosition = data.y + yNudge;
</script>

<div class='tooltip'
in:fly={{ y: 10, duration: 200, delay: 200 }}
out:fade
  style="position: absolute;
  top: {yPosition}px;
  left: {xPosition}px;"
bind:clientWidth = {tooltipwidth}
>
  <div class="county">{formatData(data)[0]}</div>
  <div class="population">{formatData(data)[1]}</div>
  <div class="change" style="background-color: {color}">{formatData(data)[2]}</div>

</div>

<style>
  .tooltip {
    position: absolute; 
    background: white;
    box-shadow: rgba(0, 0, 0, 0.15) 2px 3px 8px; /* Gives a nice 3d effect */
    padding: 8px 6px; /* Adds space around content within tooltip */
    border-radius: 3px; /* Rounds corners */
    pointer-events: none; /* Prevents tooltip from blocking mouse events */
    transition: top 300ms ease, left 300ms ease;
}

  .county {
    font-weight: bold;
    font-size: 14px;
    margin-bottom: 4px;
    padding: 3px;
  }

  .population {
    font-weight: 500;
    font-size: 12px;
    padding: 3px;
  }

  .change {
    font-size: 18px;
    color: hwb(0 100% 0%);
    padding: 3px;
    border-radius: 5px;
    font-weight: bold;
  }
</style>