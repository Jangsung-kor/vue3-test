<template>
    <div class="body">
        <canvas id="canvas1" />
    </div>
</template>
<script setup type="module">
import { createApp, watch } from 'vue'
import * as THREE from 'three'
createApp({
    name: 'Primitives',
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
// bevel을 깍아 하트 도형 만들기
function createHeart() {
    const shape = new THREE.Shape();
    const x = -1;
    const y = -2;
    shape.moveTo(x + 1, y + 1);
    shape.bezierCurveTo(x + 1, y + 1, x + 1, y, x, y);
    shape.bezierCurveTo(x - 3, y, x - 3, y + 3.5, x - 3, y + 3.5);
    shape.bezierCurveTo(x - 3, y + 5.5, x - 1.5, y + 7.7, x + 2.5, y + 9.5);
    shape.bezierCurveTo(x + 6, y + 7.7, x + 8, y + 4.5, x + 8, y + 3.5);
    shape.bezierCurveTo(x + 8, y + 3.5, x + 8, y, x + 5, y);
    shape.bezierCurveTo(x + 3.5, y, x + 2.5, y + 2.5, x + 2.5, y + 2.5);

    return new THREE.ExtrudeGeometry(shape, {
        
    })
}


function main() {
    const canvas = document.querySelector('#canvas1');
    const renderer = new THREE.WebGLRenderer({ antialias: true, canvas });
    // camera
    const fov = 70;
    const aspect = 2;
    const near = 0.1;
    const far = 1000;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.z = 12;

    // scene
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x000000);
    // geometry
    const geometrys = [
        new THREE.BoxGeometry(1, 1, 1), // 육면체(box)
        new THREE.CircleGeometry(1), // 원(circle)
        new THREE.ConeGeometry(1, 1), // 원뿔(cone)
        new THREE.CylinderGeometry(1, 1, 1), // 원통(cylinder)
        new THREE.DodecahedronGeometry(1, 1), // 십이면체(Dodecahedron)
        createHeart(),
    ]

    // 광원 추가
    {
        const color = 0xffffff; // 000000 -> 검정색, ffffff -> 흰색
        const intensity = 1;
        const light = new THREE.DirectionalLight(color, intensity);
        light.position.set(-1, 2, 4);
        scene.add(light);
    }
    {
        const color = 0xffffff; // 000000 -> 검정색, ffffff -> 흰색
        const intensity = 1;
        const light = new THREE.DirectionalLight(color, intensity);
        light.position.set(1, 2, 4);
        scene.add(light);
    }

    // 표면 속성 추가
    const material = new THREE.MeshPhongMaterial({ color: 0xe2e2e2 });

    // 메쉬 추가
    const meshes = [];
    geometrys.forEach((geometry, index) => {
        const mesh = new THREE.Mesh(geometry, material);
        mesh.position.x = -12 + index * 4;
        if (index < 7) {
            mesh.position.y = 5;
        } else if (index < 14) {
            mesh.position.y = 0;
        }
        scene.add(mesh);
        meshes.push(mesh);
    })

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

    // 애니메이션을 위한 루프 함수
    function render(time) {
        time *= 0.0005; // 1 = 1ms, 0.001 -> 1000배 느려진 것. 즉, 1s

        meshes.forEach((mesh) => {
            mesh.rotation.x = time;
            mesh.rotation.y = time;
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
<style>
.body {
    width: 100%;
    height: 100%;
}
#canvas1 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>