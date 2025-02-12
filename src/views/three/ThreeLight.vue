<template>
    <div class="body">
        <canvas id="canvas5" />
    </div>
</template>

<script setup>
import { watch } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js'
import { MTLLoader } from 'three/examples/jsm/loaders/MTLLoader.js'
import { RectAreaLightHelper } from 'three/addons/helpers/RectAreaLightHelper.js'
import { RectAreaLightUniformsLib } from 'three/addons/lights/RectAreaLightUniformsLib.js'

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
    const canvas = document.querySelector('#canvas5');
    const renderer = new THREE.WebGLRenderer({ antialias: true, canvas });
    RectAreaLightUniformsLib.init();
    renderer.shadowMap.enabled = true;

    const fov = 45;
    const aspect = 2;
    const near = 0.1;
    const far = 100;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.set(0, 10, 20);

    // OrbitControls
    const controls = new OrbitControls(camera, canvas);
    controls.enableDamping = true;
    controls.target.set(0, 5, 0);   // 시점을 중점에서 5칸 올리기
    controls.update();

    const scene = new THREE.Scene();
    scene.background = new THREE.Color('black');

    // 평면
    {
        const planeSize = 40;

        const loader = new THREE.TextureLoader();
        const texture = loader.load('https://threejs.org/manual/examples/resources/images/checker.png');
        texture.wrapS = THREE.RepeatWrapping;
        texture.wrapT = THREE.RepeatWrapping;
        texture.magFilter = THREE.NearestFilter;
        texture.colorSpace = THREE.SRGBColorSpace;
        const repeats = planeSize / 2;
        texture.repeat.set(repeats, repeats);

        const planeGeo = new THREE.PlaneGeometry(planeSize, planeSize);
        const planeMat = new THREE.MeshPhongMaterial({
            map: texture,
            side: THREE.DoubleSide,
        })
        const mesh = new THREE.Mesh(planeGeo, planeMat);
        mesh.receiveShadow = true;
        mesh.rotation.x = Math.PI * -0.5;   // XY축인 평면을 XZ축을 평면으로 하기 위해 회전시키기 90도
        scene.add(mesh);
    }
    // 벽과 천장
    {
        const cubeSize = 30;
        const cubeGeo = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
        const cubeMat = new THREE.MeshPhongMaterial({
            color: '#CCC',
            side: THREE.BackSide,
        })
        const mesh = new THREE.Mesh(cubeGeo, cubeMat);
        mesh.receiveShadow = true;
        mesh.position.set(0, cubeSize / 2 - 0.1, 0);
        scene.add(mesh);
    }
    // 정육면체
    {
        const cubeSize = 4;
        const cubeGeo = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
        const cubeMat = new THREE.MeshPhongMaterial({ color: '#8AC' });
        const mesh = new THREE.Mesh(cubeGeo, cubeMat);
        mesh.castShadow = true;
        mesh.receiveShadow = true;
        mesh.position.set(cubeSize + 1, cubeSize / 2, 0);
        scene.add(mesh);
    }
    // 구체
    {
        const sphereRadius = 3;
        const sphereWidthDivisions = 32;
        const sphereHeightDivisions = 16;
        const sphereGeo = new THREE.SphereGeometry(sphereRadius, sphereWidthDivisions, sphereHeightDivisions);
        const sphereMat = new THREE.MeshPhongMaterial({ color: '#CA8' });
        const mesh = new THREE.Mesh(sphereGeo, sphereMat);
        mesh.castShadow = true;
        mesh.receiveShadow = true;
        mesh.position.set(-sphereRadius - 2, sphereRadius + 2, 0);
        scene.add(mesh);
    }
    {
        const color = 0xFFFFFF;
        const intensity = 100;
        // pointLight : 한 점에서 무한히 뻗어나가는 광원
        const light = new THREE.PointLight(color, intensity);
        const helper = new THREE.PointLightHelper(light);
        const cameraHelper = new THREE.CameraHelper(light.shadow.camera);
        
        // 그림자 설정
        light.castShadow = true;
        
        // 조명 위치 설정 (위에서 비스듬히 비추도록)
        light.position.set(5, 15, 10);
        
        scene.add(light);
        scene.add(helper);
        scene.add(cameraHelper);
    }
    // .obj 파일
    {
        const mtlLoader = new MTLLoader();
        mtlLoader.load('/models/windmill_001.mtl', (mtl) => {
            mtl.preload();
            const objLoader = new OBJLoader();
            objLoader.setMaterials(mtl);
            objLoader.load('/models/windmill_001.obj', (root) => {
                scene.add(root);
            });
        });
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

    let renderRequested = false;
    function render() {
        renderer.render(scene, camera);
        renderRequested = false;

        if (resizeCanvasToDisplaySize(renderer)) {
            const canvas = renderer.domElement;
            camera.aspect = canvas.clientWidth / canvas.clientHeight;
            camera.updateProjectionMatrix();
        }

        controls.update();
    }
    renderer.render(scene, camera);

    function requestRenderIfNotRequested() {
        if (!renderRequested) {
            renderRequested = true;
            requestAnimationFrame(render);
        }
    }

    controls.addEventListener('change', requestRenderIfNotRequested);
    window.addEventListener('resize', requestRenderIfNotRequested);
}
</script>
<style>
.body {
    width: 100%;
    height: 100%;
}

#canvas5 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>