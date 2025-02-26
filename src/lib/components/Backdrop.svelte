<script>
    import { T, useThrelte } from '@threlte/core';
    import { PlaneGeometry } from 'three';

    let {
      floor = 1,
      receiveShadow = true,
      segments = 40,
      children,
      ...props
    } = $props();

    const easeInExpo = (x) => {
  return (x !== 0) * 2 ** (10 * x - 10);
};
  
    let geometry = $state();
    const position = $derived(() => geometry ? geometry.getAttribute('position') : null);
    const s = $derived(() => segments + 1);
    const offset = 0.5;
    const { invalidate } = useThrelte();
  
    $effect(() => {
      if (!geometry) return;
      const posAttr = geometry.getAttribute('position');
      console.log(posAttr);
      if (!posAttr || !posAttr.setXYZ) return; // Prevent undefined errors
  
      let i = 0;
      for (let x = 0; x < s(); x++) {
        for (let y = 0; y < s(); y++) {
          const xOverSegments = x / segments;
          posAttr.setXYZ(
            i,
            xOverSegments - offset + +(x === 0) * -1 * floor,
            y / segments - offset,
            easeInExpo(xOverSegments)
          );
          i += 1;
        }
      }
      posAttr.needsUpdate = true;
      geometry.computeVertexNormals();
      invalidate();
    });
  </script>
  
  <T.Group {...props}>
    {#snippet children({ ref })}
      <T.Mesh
        {receiveShadow}
        rotation.x={-0.5 * Math.PI}
        rotation.z={0.5 * Math.PI}
      >
        <T.PlaneGeometry
          args={[1, 1, segments, segments]}
          bind:ref={geometry}
        />
        {@render children?.({ ref })}
      </T.Mesh>
    {/snippet}
  </T.Group>