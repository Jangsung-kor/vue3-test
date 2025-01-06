<template>
    <div class="body">
        <canvas id="canvas4" />
    </div>
</template>
<script setup type="module">
import { createApp, watch, defineProps } from 'vue'
import * as THREE from 'three'

createApp({
    name: 'ThreeTexture',
})

const props = defineProps({
    activeTab: {
        type: Boolean,
        required: true,
        default: false,
    }
})

watch(() => props.activeTab, (active) => {
    if (active) {
        main();
    }
})

function main() {
    const canvas = document.querySelector('#canvas4');
    const renderer = new THREE.WebGLRenderer({ antialias: true, canvas });

    const fov = 70;
    const aspect = 2;
    const near = 0.1;
    const far = 5;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.z = 3;

    const scene = new THREE.Scene();

    const geometry = new THREE.BoxGeometry(1, 1, 1);

    const loader = new THREE.TextureLoader();
    const materials = [
        new THREE.MeshBasicMaterial({
            map: loadColorTexture('https://threejs.org/manual/examples/resources/images/flower-1.jpg'),
        }),
        new THREE.MeshBasicMaterial({
            map: loadColorTexture('https://threejs.org/manual/examples/resources/images/flower-2.jpg'),
        }),
        new THREE.MeshBasicMaterial({
            map: loadColorTexture('https://threejs.org/manual/examples/resources/images/flower-3.jpg'),
        }),
        new THREE.MeshBasicMaterial({
            map: loadColorTexture('https://threejs.org/manual/examples/resources/images/flower-4.jpg'),
        }),
        new THREE.MeshBasicMaterial({
            map: loadColorTexture('https://threejs.org/manual/examples/resources/images/flower-5.jpg'),
        }),
        new THREE.MeshBasicMaterial({
            map: loadColorTexture('https://threejs.org/manual/examples/resources/images/flower-6.jpg'),
        }),
    ]

    // mesh
    const meshes = [];
    const mesh = new THREE.Mesh(geometry, materials);
    scene.add(mesh);
    meshes.push(mesh);

    function loadColorTexture(path) {
        const texture = loader.load(path);
        texture.colorSpace = THREE.SRGBColorSpace;
        return texture;
    }

    function resizeCanvasToDisplaySize(renderer) {
        const canvas = renderer.domElement;
        const width = canvas.clientWidth;
        const height = canvas.clientHeight;
        const needResize = canvas.width !== width || canvas.height !== height;
        if (needResize) {
            renderer.setSize(width, height, false);
        }
        return needResize;
    }

    function render(time) {
        time *= 0.001;

        if (resizeCanvasToDisplaySize(renderer)) {
            camera.updateProjectionMatrix();
        }

        meshes.forEach((cube, ndx) => {
            const speed = 0.5 + ndx;
            const rot = time * speed;
            mesh.rotation.x = rot;
            mesh.rotation.y = rot;
        })

        renderer.render(scene, camera);
        requestAnimationFrame(render);
    }
    requestAnimationFrame(render);
}
</script>
<style>
.body {
    width: 100%;
    height: 100%;
}

#canvas4 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>