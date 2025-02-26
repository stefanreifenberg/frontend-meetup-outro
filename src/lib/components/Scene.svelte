<script>
    import { T, useTask, useThrelte } from '@threlte/core'
    import {  OrbitControls, useGltf, Environment, Float } from '@threlte/extras'
    import * as THREE from 'three'
    import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js'
    import { DRACOLoader } from 'three/addons/loaders/DRACOLoader.js'
    import { RGBELoader } from 'three/addons/loaders/RGBELoader.js'
    import { Timer } from 'three/addons/misc/Timer.js';
    import { DoubleSide } from 'three'
    import CustomShaderMaterial from 'three-custom-shader-material/vanilla'
    import wobbleVertexShader from './shaders/wobble/vertex.glsl?raw'
    import wobbleFragmentShader from './shaders/wobble/fragment.glsl?raw'
    import { mergeVertices } from 'three/addons/utils/BufferGeometryUtils.js'    
    import Backdrop from './Backdrop.svelte'
    
    /**
     * Base
     */
    // Debug
    
    const debugObject = {}
    
    // Loaders
    const rgbeLoader = new RGBELoader()
    const dracoLoader = new DRACOLoader()
    dracoLoader.setDecoderPath('./draco/')
    const gltfLoader = new GLTFLoader()
    gltfLoader.setDRACOLoader(dracoLoader)
    
    debugObject.colorA = '#6600ff'
    debugObject.colorB = '#ff0000'
    
    /**
     * Wobble
     */
    
    const uniforms = {
        uTime: new THREE.Uniform(0),
        uPositionFrequency: new THREE.Uniform(0.75),
        uTimeFrequency: new THREE.Uniform(0.4),
        uStrength: new THREE.Uniform(0.7),
    
        uWarpPositionFrequency: new THREE.Uniform(0.4),
        uWarpTimeFrequency: new THREE.Uniform(0.135),
        uWarpStrength: new THREE.Uniform(1.7),
    
        uColorA: new THREE.Uniform(new THREE.Color(debugObject.colorA)),
        uColorB: new THREE.Uniform(new THREE.Color(debugObject.colorB))
    }
    
    
    // Material
    const material = new CustomShaderMaterial({
        // CSM
        baseMaterial: THREE.MeshPhysicalMaterial,
        vertexShader: wobbleVertexShader,
        fragmentShader: wobbleFragmentShader,
        uniforms: uniforms,
        silent: true,
    
        // MeshPhysicalMaterial
        metalness: 0,
        roughness: 0.5,
        color: '#ffffff',
        transmission: 0,
        ior: 1.5,
        thickness: 1.5,
        transparent: true,
        wireframe: false
    })
    
    const depthMaterial = new CustomShaderMaterial({
        // CSM
        baseMaterial: THREE.MeshDepthMaterial,
        vertexShader: wobbleVertexShader,
        uniforms: uniforms,
        silent: true,
    
        depthPacking: THREE.RGBADepthPacking
    })
    
       
    // Geometry
    let geometry = new THREE.IcosahedronGeometry(2.5, 50)
    geometry = mergeVertices(geometry)
    geometry.computeTangents()
    
    // Mesh
    const wobble = new THREE.Mesh(geometry, material)
    wobble.customDepthMaterial = depthMaterial
    wobble.receiveShadow = true
    wobble.castShadow = true
    
    const timer = new Timer();    
    
    useTask(() => {
        timer.update()
        const elapsedTime = timer.getElapsed()
    
        uniforms.uTime.value = elapsedTime
        
    })  
    
</script>

<Backdrop scale={[50, 20, 20]} position={[0, -5, 0]} rotation={[0, 2, 0]}>
    <T.MeshStandardMaterial color="#FAFAFA" />
</Backdrop>
    
<Environment
    url="/urban_alley_01_1k.hdr"
    isBackground={false}
    format="hdr"
/>
    

<T is={wobble} />

<T.DirectionalLight
    position={[0.25, 2, - 2.25]}
    intensity={1}
    castShadow
/>

<T.PerspectiveCamera
    makeDefault
    position={[13, 3, - 2]}
    fov={45}
>
    <OrbitControls
        enableZoom={true}
        enableDamping
    />
</T.PerspectiveCamera>    