<script>
    export let yScale;
    const ticks = yScale.domain()
   
    export let groupByLocation;
    import {fade} from "svelte/transition"
    
</script>

<g class='axis y'>
    {#each ticks as tick, index}
    {#if groupByLocation}
        <g class="tick" in:fade={{delay: index*100}} out:fade>
            <text y={yScale(tick)} x={0}>
                {tick}
            </text>
        </g>
        {/if}
    {/each}
</g>

<style>
    
</style>