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
// 물레로 도형 만들기
function createLathe() {
    const points = [];
    for(let i = 0; i < 10; i++) {
        points.push(new THREE.Vector2(Math.sin(i * 0.2) * 0.3 + 3, (i - 5) * 0.8));
    }

    return new THREE.LatheGeometry(points);
}

// 매개변수 만들기
function createParametric() {
    function klein(v, u, target) {
        v *= Math.PI;
        u *= Math.PI;
        u = u * 2;

        let x;
        let z ;

        if (u <= Math.PI) {
            x = 3 * Math.cos(u) * (1 + Math.sin(u)) + (2 * (1 - Math.cos(u) / 2)) * Math.cos(u) * Math.cos(v);
            z = -8 * Math.sin(u) - 2 * (1 - Math.cos(u) / 2) * Math.sin(u) * Math.cos(v);
        } else {
            x = 3 * Math.cos(u) * (1 + Math.sin(u)) + (2 * (1 - Math.cos(u) / 2)) * Math.cos(v + Math.PI);
            z = -8 * Math.sin(u);
        }

        const y = -2 * (1 - Math.cos(u) / 2) * Math.sin(v);
        target.set(x, y, z).multiplyScalar(0.75);
    }
    const slices = 25;
    const stacks = 25;
    return new THREE.ParametricGeometry(klein, slices, stacks);
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
    camera.position.z = 36;

    // scene
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x000000);
    // geometry
    const geometrys = [
        new THREE.BoxGeometry(3, 3, 3), // 육면체(box)
        new THREE.CircleGeometry(3), // 원(circle)
        new THREE.ConeGeometry(3, 3), // 원뿔(cone)
        new THREE.CylinderGeometry(3, 3, 3), // 원통(cylinder)
        new THREE.DodecahedronGeometry(3, 3), // 십이면체(Dodecahedron)
        createHeart(),
        new THREE.IcosahedronGeometry(3), // 이십면체(Icosahedron)
        createLathe(),
        new THREE.OctahedronGeometry(3), // 팔면체(Octahedron)\
        //createParametric(),
        new THREE.PlaneGeometry(3), // 2D 평면
        new THREE.RingGeometry(1, 3), // 2D 디스크
        new THREE.TorusGeometry(3, 2, 6), // 원환체 도넛
        new THREE.TorusKnotGeometry(3, 1),  //  원환체 매듭
        new THREE.EdgesGeometry(new THREE.BoxGeometry(3, 3, 3, 2, 2, 2)),
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
        let x = -24;
        if (index < 7) {
            x = x + index * 8;
            mesh.position.x = x;
            mesh.position.y = 18;
        } else if (index < 14) {
            x = x + (index - 7) * 8
            mesh.position.x = x;
            mesh.position.y = 9;
        } else if (index < 21) {
            x = x + (index - 14) * 8
            mesh.position.x = x;
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