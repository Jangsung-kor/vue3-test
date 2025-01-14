<template>
    <div class="body">
        <canvas id="canvas8" />
    </div>
</template>
<script setup>
/*
3D 엔진에서 안개는 카메라에서 거리에 따라 특정 색상으로 점차 변화하는 것을 뜻한다.
Three.js에서는 Fog나 FogExp2 객체를 생성하고, 장면(scene)의 fog 속성을 지정한다.

Fog는 인자로 near와 far를 받는데, 이것들은 카메라로부터 거리다. 
near보다 가까운 공간은 안개가 전혀 없고, far보다 먼 공간은 안개로 완전 뒤덮인다.
near와 far 사이 공간은 점차 안개 색으로 변한다.

FogExp2는 카메라에서 멀어질수록 안개가 짙어진다.
*/
import { createApp, watch } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

createApp({
    name: 'ThreeFog',
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
    const canvas = document.querySelector('#canvas8');
    const renderer = new THREE.WebGLRenderer({
        antialias: true,
        canvas,
    });

    const fov = 75;
    const aspect = 2;
    const near = 0.1;
    const far = 5;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.z = 2;

    const controls = new OrbitControls(camera, canvas);
    controls.target.set(0, 5, 0);
    controls.update();

    const scene = new THREE.Scene();

    // 안개
    {
        const color = 'lightblue';
        const near = 1;
        const far = 3;
        scene.fog = new THREE.Fog(color, near, far);
        scene.background = new THREE.Color(color);
    }

    // 조명
    {
        const color = 0xFFFFFF;
        const intensity = 3;
        const light = new THREE.DirectionalLight(color, intensity);
        light.position.set(-1, 2, 4);
        scene.add(light);
        //const lightHelper = new THREE.DirectionalLightHelper(light);
        //scene.add(lightHelper);
    }

    // 형상
    const boxWidth = 1;
    const boxHeight = 1;
    const boxDepth = 1;
    const geometry = new THREE.BoxGeometry(boxWidth, boxHeight, boxDepth);

    function makeInstance(geometry, color, x) {
        const material = new THREE.MeshPhongMaterial({color});
        const cube = new THREE.Mesh(geometry, material);
        scene.add(cube);

        cube.position.x = x;

        return cube;
    }

    const cubes = [
        makeInstance(geometry, 0x44aa88, 0),
        makeInstance(geometry, 0x8844aa, -2),
        makeInstance(geometry, 0xaa8844, 2),
    ]

    function resizeRendererToDisplaySize(renderer) {
        const canvas = renderer.domElement;
        const width = canvas.clientWidth;
        const height = canvas.clientHeight;
        const needResize = canvas.width != width || canvas.height !== height;
        if(needResize) {
            renderer.setSize(width, height, false);
        }

        return needResize;
    }

    function render(time) {
        time *= 0.001;
        if (resizeRendererToDisplaySize(renderer)) {
            const canvas = renderer.domElement;
            camera.aspect = canvas.clientWidth / canvas.clientHeight;
            camera.updateProjectionMatrix();
        }

        cubes.forEach((cube, ndx) => {
            const speed = 1 + ndx * 0.1;
            const rot = time * speed;
            cube.rotation.x = rot;
            cube.rotation.y = rot;
        });

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
#canvas8 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>