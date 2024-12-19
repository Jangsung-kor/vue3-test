<template>
    <h1>three.js에 오신 걸 환영합니다.</h1>
    <div>
        <canvas id="canvas" />
    </div>
</template>
<script setup type="module">
import { createApp, onMounted } from 'vue'
import * as THREE from 'three'

createApp({
    name: 'HelloThree',
})

function main() {
    const canvas = document.querySelector('#canvas');
    // renderer
    const renderer = new THREE.WebGLRenderer({antialias: true, canvas})
    // camera
    const fov = 75; // field of view : 시야각 -> 수직면 75도
    const aspect = 2; // 가로 세로 비율 -> 2:1
    const near = 0.1; // 카메라 가까운 범위?
    const far = 5; // 카메라 먼 범위?
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.z = 2; // 카메라는 z = 2 위치에서 -z 방향으로 보고 있음
    // scene
    const scene = new THREE.Scene();
    // geometry
    const boxWidth = 1;
    const boxHeight = 1;
    const boxDepth = 1;
    const geometry = new THREE.BoxGeometry(boxWidth, boxHeight, boxDepth);
    // material
    //const material = new THREE.MeshBasicMaterial({color: 0xe2e2e2}); // MeshBasiicMaterial은 광원에 반응하지 않음.
    const material = new THREE.MeshPhongMaterial({color: 0xe2e2e2});
    // mesh
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);
    // 광원 추가
    const color = 0xffffff;
    const intensity = 1;
    const light = new THREE.DirectionalLight(color, intensity);
    light.position.set(-1, 2, 4);
    scene.add(light);
    // 애니메이션을 위한 루프 함수
    function render( time ) {
        time *= 0.001; // 1 = 1ms, 0.001 -> 1000배 느려진 것. 즉, 1s
        
        cube.rotation.x = time; // 1바퀴당 6.28초 (1초 * 2파이)
        cube.rotation.y = time; // 1바퀴당 6.28초 (1초 * 2파이)

        renderer.render(scene, camera);
        requestAnimationFrame(render);
    }
    requestAnimationFrame(render);
}


onMounted(() => {
    main();    
})
</script>