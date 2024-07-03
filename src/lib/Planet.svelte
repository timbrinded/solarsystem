<!-- src/lib/Planet.svelte -->
<script>
    export let name;
    export let color;
    export let size;
  
    import { fade } from 'svelte/transition';
  
    let hover = false;
  </script>
  
  <div
    class="planet"
    style="background-color: {color}; width: {size}px; height: {size}px;"
    on:mouseenter={() => hover = true}
    on:mouseleave={() => hover = false}
    on:click
  >
    {#if hover}
      <div class="name" transition:fade>
        {name}
      </div>
    {/if}
  </div>
  
  <style>
    .planet {
      border-radius: 50%;
      position: relative;
      cursor: pointer;
    }
  
    .name {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background-color: rgba(0, 0, 0, 0.7);
      padding: 5px;
      border-radius: 5px;
      white-space: nowrap;
    }
  </style>