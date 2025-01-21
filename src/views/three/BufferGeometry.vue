<template>
    <div class="body">
        <canvas id="canvas10" />
    </div>
</template>
<script setup>
/*
BufferGeoemtry란 geometry를 뜻한다. 자세히 말하면 특정 BufferAttribute라고 부르는 속성의 집합이다.
BufferAttribute는 위치(position), 법선(normals), 색(color), uv 데이터의 배열이다.
*/
import { createApp, watch } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

createApp({
    name: 'BufferGeometry',
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
    const canvas = document.querySelector('#canvas10');
    const renderer = new THREE.WebGLRenderer({
        antialias: true,
        canvas,
    })

    const fov = 75;
    const aspect = 2;
    const near = 0.1;
    const far = 100;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.z = 5;

    const controls = new OrbitControls(camera, canvas);
    controls.target.set(0, 0, 0);
    controls.update();

    const scene = new THREE.Scene();

    // 조명
    {
        const color = 0xFFFFFF;
        const intensity = 3;
        const light = new THREE.DirectionalLight(color, intensity);
        light.position.set(-1, 2, 4);
        scene.add(light);
    }

    const vertices = [
        // 앞쪽
        { pos: [-1, -1, 1], norm: [0, 0, 1], uv: [0, 0], },
        { pos: [1, -1, 1], norm: [0, 0, 1], uv: [1, 0], },
        { pos: [-1, 1, 1], norm: [0, 0, 1], uv: [0, 1], },

        { pos: [-1, 1, 1], norm: [0, 0, 1], uv: [0, 1], },
        { pos: [1, -1, 1], norm: [0, 0, 1], uv: [1, 0], },
        { pos: [1, 1, 1], norm: [0, 0, 1], uv: [1, 1], },
        // 오른쪽
        { pos: [1, -1, 1], norm: [1, 0, 0], uv: [0, 0], },
        { pos: [1, -1, -1], norm: [1, 0, 0], uv: [1, 0], },
        { pos: [1, 1, 1], norm: [1, 0, 0], uv: [0, 1], },

        { pos: [1, 1, 1], norm: [1, 0, 0], uv: [0, 1], },
        { pos: [1, -1, -1], norm: [1, 0, 0], uv: [1, 0], },
        { pos: [1, 1, -1], norm: [1, 0, 0], uv: [1, 1], },
        // 뒤쪽
        { pos: [1, -1, -1], norm: [0, 0, -1], uv: [0, 0], },
        { pos: [-1, -1, -1], norm: [0, 0, -1], uv: [1, 0], },
        { pos: [1, 1, -1], norm: [0, 0, -1], uv: [0, 1], },

        { pos: [1, 1, -1], norm: [0, 0, -1], uv: [0, 1], },
        { pos: [-1, -1, -1], norm: [0, 0, -1], uv: [1, 0], },
        { pos: [-1, 1, -1], norm: [0, 0, -1], uv: [1, 1], },
        // 왼쪽
        { pos: [-1, -1, -1], norm: [-1, 0, 0], uv: [0, 0], },
        { pos: [-1, -1, 1], norm: [-1, 0, 0], uv: [1, 0], },
        { pos: [-1, 1, -1], norm: [-1, 0, 0], uv: [0, 1], },

        { pos: [-1, 1, -1], norm: [-1, 0, 0], uv: [0, 1], },
        { pos: [-1, -1, 1], norm: [-1, 0, 0], uv: [1, 0], },
        { pos: [-1, 1, 1], norm: [-1, 0, 0], uv: [1, 1], },
        // 상단
        { pos: [1, 1, -1], norm: [0, 1, 0], uv: [0, 0], },
        { pos: [-1, 1, -1], norm: [0, 1, 0], uv: [1, 0], },
        { pos: [1, 1, 1], norm: [0, 1, 0], uv: [0, 1], },

        { pos: [1, 1, 1], norm: [0, 1, 0], uv: [0, 1], },
        { pos: [-1, 1, -1], norm: [0, 1, 0], uv: [1, 0], },
        { pos: [-1, 1, 1], norm: [0, 1, 0], uv: [1, 1], },
        // 하단
        { pos: [1, -1, 1], norm: [0, -1, 0], uv: [0, 0], },
        { pos: [-1, -1, 1], norm: [0, -1, 0], uv: [1, 0], },
        { pos: [1, -1, -1], norm: [0, -1, 0], uv: [0, 1], },

        { pos: [1, -1, -1], norm: [0, -1, 0], uv: [0, 1], },
        { pos: [-1, -1, 1], norm: [0, -1, 0], uv: [1, 0], },
        { pos: [-1, -1, -1], norm: [0, -1, 0], uv: [1, 1], },
    ];

    const positions = [];
    const normals = [];
    const uvs = [];
    for (const vertex of vertices) {
        positions.push(...vertex.pos);
        normals.push(...vertex.norm);
        uvs.push(...vertex.uv);
    }

    // BufferGeometry 생성
    const geometry = new THREE.BufferGeometry();
    const positionNumComponents = 3;
    const normalNumComponents = 3;
    const uvNumComponents = 2;
    geometry.setAttribute(
        'position',
        new THREE.BufferAttribute(new Float32Array(positions), positionNumComponents)
    );
    geometry.setAttribute(
        'normal',
        new THREE.BufferAttribute(new Float32Array(normals), normalNumComponents)
    );
    geometry.setAttribute(
        'uv',
        new THREE.BufferAttribute(new Float32Array(uvs), uvNumComponents)
    );

    const loader = new THREE.TextureLoader();
    const texture = loader.load('https://threejs.org/manual/examples/resources/images/star.png');
    texture.colorSpace = THREE.SRGBColorSpace;

    function makeInstance(geometry, color, x) {
        const material = new THREE.MeshPhongMaterial({ color, map: texture });
        const cube = new THREE.Mesh(geometry, material);
        scene.add(cube);

        cube.position.x = x;
        return cube;
    }

    // const cubes = [
    //     makeInstance(geometry, 0x88FF88, 0),
    //     makeInstance(geometry, 0x8888FF, -4),
    //     makeInstance(geometry, 0xFF8888, 4),
    // ];

    makeInstance(geometry, 0x88FF88, 0);
    makeInstance(geometry, 0x8888FF, -4);
    makeInstance(geometry, 0xFF8888, 4);

    function resizeRendererToDisplaySize(renderer) {
        const canvas = renderer.domElement;
        const width = canvas.clientWidth;
        const height = canvas.clientHeight;
        const needResize = canvas.width != width || canvas.height !== height;
        if (needResize) {
            renderer.setSize(width, height, false);
        }

        return needResize;
    }

    function render() {

        // time *= 0.001;

        if (resizeRendererToDisplaySize(renderer)) {

            const canvas = renderer.domElement;
            camera.aspect = canvas.clientWidth / canvas.clientHeight;
            camera.updateProjectionMatrix();

        }

        // cubes.forEach((cube, ndx) => {

        //     const speed = 1 + ndx * .1;
        //     const rot = time * speed;
        //     cube.rotation.x = rot;
        //     cube.rotation.y = rot;

        // });

        renderer.render(scene, camera);

        // requestAnimationFrame(render);

    }

    // requestAnimationFrame(render);
    render();

    controls.addEventListener('change', render);
    window.addEventListener('resize', render);
}
</script>
<style>
.body {
    width: 100%;
    height: 100%;
}

#canvas10 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>