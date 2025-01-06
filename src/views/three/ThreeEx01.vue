<template>
    <div class="body">
        <canvas id="canvas" />
    </div>
    <div class="control">
        <div>
            Drag this bar
        </div>
        <div>
            &lt;=
        </div>
    </div>
</template>
<script setup type="module">
import { createApp, watch } from 'vue'
import * as THREE from 'three'

createApp({
    name: 'ThreeEx01',
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
    const canvas = document.querySelector('#canvas'); // css 셀렉터
    // renderer
    const renderer = new THREE.WebGLRenderer({ antialias: true, canvas })
    // camera
    const fov = 75; // field of view : 시야각 -> 수직면 75도
    const aspect = 2; // 가로 세로 비율 -> 2:1
    const near = 0.1; // 카메라 가까운 범위?
    const far = 5; // 카메라 먼 범위?
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.z = 5; // 카메라는 z = 2 위치에서 -z 방향으로 보고 있음

    // scene
    const scene = new THREE.Scene();
    // geometry
    const boxWidth = 1;
    const boxHeight = 1;
    const boxDepth = 1;
    const geometry = new THREE.BoxGeometry(boxWidth, boxHeight, boxDepth);

    // 광원 추가
    const color = 0xffffff;
    const intensity = 1;
    const light = new THREE.DirectionalLight(color, intensity);
    light.position.set(-1, 2, 4);
    scene.add(light);

    // Mesh 만들어주는 함수 : 1개의 geometry로 여러 material을 가진 메쉬 생성
    function makeInstance(geometry, color, x, y) {
        // 표면 속성 생성
        const material = new THREE.MeshPhongMaterial({ color });
        // 메쉬 생성
        const cube = new THREE.Mesh(geometry, material);
        cube.position.x = x;
        cube.position.y = y;
        scene.add(cube);

        return cube;
    }

    const cubes = [
        makeInstance(geometry, 0xe2e2e2, 0, 0),
        makeInstance(geometry, 0x44aa88, -2, 0),
        makeInstance(geometry, 0xaa8844, 2, 0),

        makeInstance(geometry, 0xe2e2e2, -2, -2),
    ]

    // canvas 원본 크기와 디스플레이(CSS) 크기를 비교해 크기를 변경할지 결정하는 함수
    function resizeCanvasToDisplaySize(renderer) {
        const canvas = renderer.domElement;
        const width = canvas.clientWidth;
        const height = canvas.clientHeight;
        // canvas 리사이징할 필요가 검사하는 이유는 canvas는 화면을 다시 랜더링하므로 리사이징할 필요가 없을때는 불필요한 자원 낭비를 막아야 한다.
        const needResize = canvas.width !== width || canvas.height !== height;
        if (needResize) {
            // 마지막 인자로 false를 넣어주면 three.js가 자동으로 CSS를 제어하는 것을 막아준다.
            renderer.setSize(width, height, false);
        }
        return needResize;
    }

    // 애니메이션을 위한 루프 함수
    function render(time) {
        time *= 0.001; // 1 = 1ms, 0.001 -> 1000배 느려진 것. 즉, 1s

        cubes.forEach((cube, ndx) => {
            const speed = 1 + ndx * 0.1;
            const rot = time * speed;
            cube.rotation.x = rot;
            cube.rotation.y = rot;
        })

        if (resizeCanvasToDisplaySize(renderer)) {
            const canvas = renderer.domElement;
            camera.aspect = canvas.clientWidth / canvas.clientHeight;
            camera.updateProjectionMatrix();
        }

        renderer.render(scene, camera);
        requestAnimationFrame(render);
    }
    requestAnimationFrame(render);
}
</script>
<style scoped>
.body {
    margin: 0;
    height: 100%;
}

#canvas {
    width: 100%;
    height: 100%;
    display: block;
}
</style>
<!--
DPI란 dot per inch의 약자로 1인치당 점 개수를 뜻하며, 해상도와 괸련이 있다. 점 개수가 많을 수록 고해상도다.
PPI란 pixel per inch의 약자로 1인치당 픽셀 개수를 뜻하며, 해상도와 괸련이 있다. 픽셀 개수가 많을 수록 고해상도다.

HD-DPI란 higt density dot per inch의 약���로 고해상도를 뜻한다.

Three.js로 HD-DPI를 다루는 방법
1. 아무것도 하지 않는다.
 : 3D 랜더링은 많은 GPU를 사용하기 때문에 많은 픽셀을 그대로 계산하면 성능이 떨어진다.
 예를 들어 화면을 버벅거리게 만들고 고사양 게임을 하면 프레임이 떨어진다. 그러므로 이 방법은 추천하지 않는다.
 render.setPixelRatio(window.devicePixelRatio);
 render의 해상도 배율을 지정한다. 그러면 canvas는 사이즈에 배율을 곱해서 리사이징한다.

2. canvas를 리사이징할 때 직접 계산한다.
function resizeCanvasToDisplaySize(renderer) {
    const canvas = renderer.domElement;
    const pixelRatio = window.devicePixelRatio;
    const width = Math.floor(canvas.clientWidth * pixelRatio);
    const height = Math.floor(canvas.clientHeight * pixelRatio);
    const needResize = canvas.width !== width || canvas.height !== height;
    if (needResize) {
        renderer.setSize(width, height, false);
    }
    return needResize;
}
-->