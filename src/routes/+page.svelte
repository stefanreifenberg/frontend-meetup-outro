<script>
    import { Canvas } from '@threlte/core'
    import Scene from '$lib/components/Scene.svelte'
    import Underlay from '$lib/components/Underlay.svelte'
    import Overlay from '$lib/components/Overlay.svelte'
  </script>
  
 
<div class="container">
    <!-- Underlay (behind the canvas) -->
    <div class="underlay-wrapper">
        <Underlay />
    </div>

    <!-- Canvas (middle layer) -->
    <div class="canvas-wrapper">
        <Canvas shadows>
                <!-- <Debug /> -->
                <Scene />
           
        </Canvas>
    </div>

    <!-- Overlay (above the canvas) -->
    <div class="overlay-wrapper">
        <Overlay />
    </div>
</div>

<style>
    .container {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }

    .underlay-wrapper {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 1;
    }

    .canvas-wrapper {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 0;
    }

    .overlay-wrapper {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 10;
        pointer-events: none;
    }
    :global(.overlay-wrapper a ){
        pointer-events: auto;
    }
</style>