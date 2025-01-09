<template>
    <div class="body">
        <canvas id="canvas5" />
    </div>
</template>

<script setup>
import { watch } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
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

    const fov = 45;
    const aspect = 2;
    const near = 0.1;
    const far = 100;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.set(0, 10, 20);

    // OrbitControls
    const controls = new OrbitControls(camera, canvas);
    controls.target.set(0, 5, 0);   // 시점을 중점에서 5칸 올리기
    controls.update();

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
    // const planeMat = new THREE.MeshPhongMaterial({
    //     map: texture,
    //     side: THREE.DoubleSide,
    // })
    const planeMat = new THREE.MeshStandardMaterial({
        map: texture,
        side: THREE.DoubleSide,
    })
    const mesh = new THREE.Mesh(planeGeo, planeMat);
    mesh.rotation.x = Math.PI * -0.5;   // XY축인 평면을 XZ축을 평면으로 하기 위해 회전시키기 90도

    const scene = new THREE.Scene();
    scene.add(mesh);

    // 정육면체와 구체도 추가 , 총 3개의 물체
    {
        const cubeSize = 4;
        const cubeGeo = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
        //const cubeMat = new THREE.MeshPhongMaterial({color: '#8AC'});
        const cubeMat = new THREE.MeshStandardMaterial({ color: '#8AC' })
        const mesh = new THREE.Mesh(cubeGeo, cubeMat);
        mesh.position.set(cubeSize + 1, cubeSize / 2, 0);
        scene.add(mesh);
    }
    {
        const sphereRadius = 3;
        const sphereWidthDivisions = 32;
        const sphereHeightDivisions = 16;
        const sphereGeo = new THREE.SphereGeometry(sphereRadius, sphereWidthDivisions, sphereHeightDivisions);
        //const sphereMat = new THREE.MeshPhongMaterial({color: '#CA8'});
        const sphereMat = new THREE.MeshStandardMaterial({ color: '#CA8' });
        const mesh = new THREE.Mesh(sphereGeo, sphereMat);
        mesh.position.set(-sphereRadius - 1, sphereRadius + 2, 0);
        scene.add(mesh);
    }
    {
        const color = 0xFFFFFF;
        const intensity = 5;
        // AmbientLight(자연광) : 방향이 없는 빛이다. 모니터 밝기 느낌.
        //const light = new THREE.AmbientLight(color, intensity);
        // HemisphereLight(반구광) : 자연광의 특성에서 위쪽과 아래쪽 색상이 변경가능한 정도.
        const skyColor = 0xB1E1FF; // 하늘색
        const groundColor = 0xB97a20; // 오렌지 브라운
        //const light = new THREE.HemisphereLight(skyColor, groundColor, intensity);
        // DirectionalLight(직사광) : 태양 빛 느낌.
        //const light = new THREE.DirectionalLight(color, intensity);
        //const helper = new THREE.DirectionalLightHelper(light);
        // pointLight : 한 점에서 무한히 뻗어나가는 광원
        //const light = new THREE.PointLight(color, intensity);
        //const helper = new THREE.PointLightHelper(light);
        // spotLight : 원뿔 모양의 pointLight
        //const light = new THREE.SpotLight(color, intensity);
        //const helper = new THREE.SpotLightHelper(light);
        // rectAreaLight : 사각 조명. 형광등이나 유리를 통과하는 태양빛. 
        // rectAreaLight는 MeshStandardMaterial과 MeshPhysicalMaterial만 지원
        const width = 12;
        const height = 4;
        const light = new THREE.RectAreaLight(color, intensity, width, height);
        light.position.set(0, 10, 0);
        //light.target.position.set(-5, 0, 0);
        light.rotation.x = THREE.MathUtils.degToRad(-90);
        const helper = new RectAreaLightHelper(light);
        scene.add(light);
        //scene.add(light.target);
        scene.add(helper);

        function updateLight() {
            light.target.updateMatrixWorld();
            helper.update();
        }

        //updateLight();
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

    function render() {
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

#canvas5 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>