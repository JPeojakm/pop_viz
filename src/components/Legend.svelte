<script>
    export let colorScale;
    export let hoveredLocation;
    
    const ticks = colorScale.domain();
</script>

<div class='legend' on:mouseleave={() => (hoveredLocation = null)}>
    {#each colorScale.domain() as location}
      <p on:mouseover={() => (hoveredLocation = location)}
        class:unhovered={hoveredLocation && hoveredLocation !== location}>
        <span style="background-color: {colorScale(location)}" />
        {location}
      </p> 
    {/each}
  </div>

  <style>
   .legend {
    display: flex;
    flex-direction: row; /* Makes the legend horizontal */
    justify-content: center; /* Centers the legend items */
    flex-wrap: wrap; /* Wraps the legend items to the next line */
    column-gap: 10px;
    row-gap: 5px;
    margin-bottom: 0.25rem;
   }

   span{
    width: 9px;
    height: 9px;
    display: inline-block; /* Makes the span behave like an inline element */
    border-radius: 50%; /* Makes the span a circle */
    border: 1px solid rgba(0, 0, 0, 0.5); /* Adds a slight border */
   }

   .unhovered {
    opacity: 0.3;
}

p {
    /* Alongside other styling for p elements */
    transition: opacity 300ms ease;
    cursor: pointer;
}
  </style>