<!-- src/lib/SolarSystem.svelte -->
<script>
    import { T, useFrame, useLoader } from '@threlte/core';
    import { createEventDispatcher, onMount } from 'svelte';
    import * as THREE from 'three';
  
    const dispatch = createEventDispatcher();
  
    let planets = [
      { name: 'Sun', size: 100, orbitRadius: 0, orbitSpeed: 0, texturePath: '/textures/sun.jpg' },
      { name: 'Mercury', size: 30, orbitRadius: 28, orbitSpeed: 1.7, texturePath: '/textures/mercury.jpg' },
      { name: 'Venus', size:20, orbitRadius: 44, orbitSpeed: 0.7, texturePath: '/textures/venus.jpg' },
      { name: 'Earth', size: 20, orbitRadius: 62, orbitSpeed: 0.2, texturePath: '/textures/earth.jpg' },
      { name: 'Mars', size: 15, orbitRadius: 78, orbitSpeed: 0.6, texturePath: '/textures/mars.jpg' },
    ];
  
    let time = 0;
    let textures = [];
  
    onMount(async () => {
      const loader = new THREE.TextureLoader();
      
      for (let planet of planets) {
        try {
          const texture = await loader.loadAsync(planet.texturePath);
          textures.push(texture);
          console.log(`Loaded texture for ${planet.name}`);
        } catch (error) {
          console.error(`Failed to load texture for ${planet.name}:`, error);
          // Push a default texture or null
          textures.push(null);
        }
      }
      textures = textures; // Trigger reactivity
    });
  
    useFrame((state, delta) => {
      time += delta;
      planets = planets.map(planet => ({
        ...planet,
        position: [
          Math.cos(time * planet.orbitSpeed) * planet.orbitRadius,
          0,
          Math.sin(time * planet.orbitSpeed) * planet.orbitRadius
        ]
      }));
    });
  
    function selectPlanet(planet) {
      dispatch('selectPlanet', planet);
    }
  
    function getColor(planetName) {
      const colorMap = {
        'Sun': '#FDB813',
        'Mercury': '#888888',
        'Venus': '#e39e1c',
        'Earth': '#2b82c9',
        'Mars': '#c0392b'
      };
      return colorMap[planetName] || '#ffffff';
    }
  </script>
  
  {#each planets as planet, i (planet.name)}
    <T.Group>
      <T.Mesh 
        position={planet.position} 
        scale={planet.size / 10}
        on:click={() => selectPlanet(planet)}
      >
        <T.SphereGeometry args={[1, 64, 32]} />
        {#if textures[i]}
          <T.MeshStandardMaterial map={textures[i]} />
        {:else}
          <T.MeshStandardMaterial color={getColor(planet.name)} />
        {/if}
      </T.Mesh>
      {#if planet.name !== 'Sun'}
        <T.Line>
          <T.BufferGeometry>
            <T.Float32BufferAttribute attach="attributes-position" args={[new Float32Array(64 * 3), 3]} />
          </T.BufferGeometry>
          <T.LineBasicMaterial color="#666666" opacity={0.25} transparent={true} />
        </T.Line>
      {/if}
    </T.Group>
  {/each}