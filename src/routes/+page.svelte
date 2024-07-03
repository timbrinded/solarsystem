<!-- src/routes/+page.svelte -->
<script>
    import { onMount } from 'svelte';
    import { fly } from 'svelte/transition';
    import { browser } from '$app/environment';
  
    let Canvas;
    let T;
    let OrbitControls;
    let SolarSystem;
    
    onMount(async () => {
      if (browser) {
        const threlte = await import('@threlte/core');
        const threlteExtras = await import('@threlte/extras');
        Canvas = threlte.Canvas;
        T = threlte.T;
        OrbitControls = threlteExtras.OrbitControls;
        SolarSystem = (await import('$lib/SolarSystem.svelte')).default;
      }
    });
  
    let selectedPlanet = null;
    let showInfo = false;
    let showControls = true;
  
    function handlePlanetSelect(event) {
      selectedPlanet = event.detail;
      showInfo = true;
    }
  </script>
  <svelte:head>
    <title>Svelte 3D Space Explorer</title>
  </svelte:head>
  
  <main>
    <h1>Svelte 3D Space Explorer</h1>
    
    {#if browser && Canvas && T && OrbitControls && SolarSystem}
      <div class="canvas-container">
        <Canvas>
          <T.PerspectiveCamera makeDefault position={[0, 100, 150]} fov={75}>
            <OrbitControls enableZoom={true} enablePan={true} enableRotate={true} />
          </T.PerspectiveCamera>
          
          <T.AmbientLight intensity={0.5} />
          <T.DirectionalLight intensity={1} position={[5, 5, 5]} />
  
          <SolarSystem on:selectPlanet={handlePlanetSelect} />
        </Canvas>
      </div>
    {:else}
      <p>Loading 3D environment...</p>
    {/if}
  
    <!-- ... (rest of the component remains the same) -->
  </main>
  
  <style>
    main {
      width: 100vw;
      height: 100vh;
      background-color: #0f0f1f;
      color: white;
      overflow: hidden;
      position: relative;
    }
  
    h1 {
      position: absolute;
      top: 20px;
      left: 20px;
      z-index: 10;
    }
  
    .canvas-container {
      width: 100%;
      height: 100%;
    }
  
    .info-panel, .controls-panel {
      position: absolute;
      background-color: rgba(255, 255, 255, 0.1);
      padding: 20px;
      border-radius: 10px;
      z-index: 20;
    }
  
    .info-panel {
      bottom: 20px;
      left: 50%;
      transform: translateX(-50%);
    }
  
    .controls-panel {
      top: 20px;
      right: 20px;
      max-width: 300px;
    }
  
    ul {
      padding-left: 20px;
    }
  
    button {
      background-color: #4CAF50;
      border: none;
      color: white;
      padding: 10px 20px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      font-size: 16px;
      margin: 4px 2px;
      cursor: pointer;
      border-radius: 5px;
    }
  </style>