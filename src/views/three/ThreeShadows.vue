<template>
    <div class="body">
        <canvas id="canvas7" />
    </div>
</template>
<script setup>
import { createApp, watch } from 'vue';
import * as THREE from 'three';

createApp({
    name: 'ThreeShadows',
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
    const canvas = document.querySelector('#canvas7');
    const renderer = new THREE.WebGLRenderer({
        antialias: true,
        canvas,
    })
    renderer.shadowMap.enabled = true;  // 그림자 맵 ON

    const fov = 45;
    const aspect = 2;
    const near = 0.1;
    const far = 100;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.set(0, 10, 20);
    camera.lookAt(0, 0, 0,);

    const scene = new THREE.Scene();
    scene.background = new THREE.Color('white');

    const loader = new THREE.TextureLoader();
    
    // 평면 생성
    {
        // 텍스쳐
        const planeSize = 40;
        const texture = loader.load('https://threejs.org/manual/examples/resources/images/checker.png');
        texture.wrapS = THREE.RepeatWrapping;   // 수평 반복
        texture.wrapT = THREE.RepeatWrapping;   // 수직 반복
        texture.magFilter = THREE.NearestFilter;    // 원본보다 클 때 가장 가까운 픽셀을 선택한다.
        const repeats = planeSize / 2;
        texture.repeat.set(repeats, repeats);

        // 도형과 질감
        const planeGeo = new THREE.PlaneGeometry(planeSize, planeSize);
        const planeMat = new THREE.MeshBasicMaterial({
            map: texture,
            side: THREE.DoubleSide,
        });
        planeMat.color.setRGB(1.5, 1.5, 1.5);
        const mesh = new THREE.Mesh(planeGeo, planeMat);
        mesh.receiveShadow = true;
        mesh.rotation.x = Math.PI * -0.5;
        scene.add(mesh);
    }

    // 그림자 텍스처
    const shadowTexture = loader.load('https://threejs.org/manual/examples/resources/images/roundshadow.png');
    // 구체와 관련된 객체를 분류하기 위한 배열
    const sphereShadowsBases = [];
    // 구체
    const sphereRadius = 1;
    const sphereWidthDivisions = 32;
    const sphereHeightDivisions = 16;
    const sphereGeo = new THREE.SphereGeometry(sphereRadius, sphereWidthDivisions, sphereHeightDivisions);
    
    // 가짜 그림자를 위한 평면
    const planeSize = 1;
    const shadowGeo = new THREE.PlaneGeometry(planeSize, planeSize);

    const numSpheres = 15;
    for (let i = 0; i < numSpheres; i++) {
        // 구체와 그림자가 같이 움직이는 컨테이너(base)
        const base = new THREE.Object3D();
        scene.add(base);

        // 그림자를 컨테이너에 추가
        const shadowMat = new THREE.MeshBasicMaterial({
            map: shadowTexture,
            transparent: true,  // 땅이 보임
            depthWrite: false,  // 그림자 끼리 충돌하는 현상 막기
        })
        const shadowMesh = new THREE.Mesh(shadowGeo, shadowMat);
        shadowMesh.position.y = 0.001;  // 그림자를 땅에서 살짝 떠 있도록
        shadowMesh.rotation.x = Math.PI * -0.5;
        const shadowSize = sphereRadius * 4;
        shadowMesh.scale.set(shadowSize, shadowSize, shadowSize);
        base.add(shadowMesh);

        // 구체를 컨테이너에 추가
        const u = i / numSpheres;
        const sphereMat = new THREE.MeshPhongMaterial();
        sphereMat.color.setHSL(u, 1, 0.75);
        const sphereMesh = new THREE.Mesh(sphereGeo, sphereMat);
        sphereMesh.position.set(0, sphereRadius + 2, 0);
        base.add(sphereMesh);

        // y축 좌표를 퐇마해 나머지 요소를 기록
        sphereShadowsBases.push({
            base,
            sphereMesh,
            shadowMesh,
            y: sphereMesh.position.y
        });
    }

    // 조명 2개 추가
    // HemisphereLight
    {
        const skyColor = 0xB1E1FF;
        const groundColor = 0xB97A20;
        const intensity = 2;
        const light = new THREE.HemisphereLight(skyColor, groundColor, intensity);
        scene.add(light);
    }
    // DirectionalLight : 태양 빛
    {
        const color = 0xFFFFFF;
        const intensity = 1;
        const light = new THREE.DirectionalLight(color, intensity);
        light.castShadow = true;
        light.position.set(0, 10, 5);
        light.target.position.set(-5, 0, 0);
        scene.add(light);
        scene.add(light.target);
    }

    function resizeRendererToDisplaySize(renderer) {
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
        time *= 0.001;  // 초 단위로 변환

        resizeRendererToDisplaySize(renderer);

        sphereShadowsBases.forEach((sphereShadowBase, ndx) => {
            const { base, sphereMesh, shadowMesh, y} = sphereShadowBase;
            
            const u = ndx / sphereShadowsBases.length;

            // 컨테이너 위치를 계산한다. 구체와 그림자가 컨테이너에 종속적이므로 위차가 같이 변한다.
            const speed = time * 0.2;
            const angle = speed + u * Math.PI * 2 * (ndx % 1 ? 1 : -1);
            const radius = Math.sin(speed - ndx) * 10;
            base.position.set(Math.cos(angle) * radius, 0, Math.sin(angle) * radius);

            const yOff = Math.abs(Math.sin(time * 2 + ndx));
            // 구체를 위 아래로 튕김
            sphereMesh.position.y = y + THREE.MathUtils.lerp(-2, 2, yOff);
            // 구체가 위로 올라갈수록 그림자 옅어짐
            shadowMesh.material.opacity = THREE.MathUtils.lerp(1, 0.25, yOff);
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
#canvas7 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>