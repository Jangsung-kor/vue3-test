<template>
    <div>
        <canvas id="canvas2" />
    </div>
</template>
<script setup type="module">
import { createApp, watch } from 'vue'
import * as THREE from 'three'
import GUI from 'lil-gui'

createApp({
    name: 'SceneGraph',
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
    const canvas = document.querySelector('#canvas2');
    const renderer = new THREE.WebGLRenderer({ antialias: true, canvas });
    const scene = new THREE.Scene();
    const gui = new GUI();

    // 회전값을 업데이트할 객체들
    const objects = [];

    // 구 생성 : 달, 지구 ,태영
    const radius = 1;
    const widthSegments = 6; // 적도를 중심으로 6분할
    const heightSegments = 6; // 적도를 중심으로 6분할
    const sphereGeometry = new THREE.SphereGeometry(radius, widthSegments, heightSegments);

    // 태양계 생성
    const solarSystem = new THREE.Object3D();
    scene.add(solarSystem);
    objects.push(solarSystem);

    // 태양 생성
    const sunMaterial = new THREE.MeshPhongMaterial({ emissive: 0xffff00 }); // emissive : 빛을 반사하지 않는 표면.
    const sunMesh = new THREE.Mesh(sphereGeometry, sunMaterial);
    sunMesh.scale.set(5, 5, 5); // 태양 크기 키움 : 5배
    solarSystem.add(sunMesh);
    objects.push(sunMesh);

    // 지구계 생성
    const earthSystem = new THREE.Object3D();
    earthSystem.position.x = 10;
    solarSystem.add(earthSystem);
    objects.push(earthSystem);

    // 지구 생성
    const earthMaterial = new THREE.MeshPhongMaterial({ color: 0x2233ff, emissive: 0x112244 });
    const earthMesh = new THREE.Mesh(sphereGeometry, earthMaterial);
    earthSystem.add(earthMesh);
    objects.push(earthMesh);

    // 달계 생성
    const moonSystem = new THREE.Object3D();
    moonSystem.position.x = 2;
    earthSystem.add(moonSystem);
    objects.push(moonSystem);

    // 달 생성
    const moonMaterial = new THREE.MeshPhongMaterial({ color: 0x888888, emissive: 0x222222 });
    const moonMesh = new THREE.Mesh(sphereGeometry, moonMaterial);
    moonMesh.scale.set(0.5, 0.5, 0.5);
    moonSystem.add(moonMesh);
    objects.push(moonMesh);

    /*
    축과 격자를 동시에 켜고 끈다. 
    lil-gui가 체크박스를 만들게 하려면 boolean 타입의 속성을 지정해줘야 한다.
    'visible' 속성에 getter와 setter를 지정해 lil-gui가 이 속성을 바라보도록 한다.
    */
    class AxisGridHelper {
        constructor(object, units = 10) {
            const axes = new THREE.AxesHelper();
            axes.material.depthTest = false;
            axes.renderOrder = 2; // 격자 다음에 렌더링
            object.add(axes);

            const grid = new THREE.GridHelper(units, units);
            grid.material.depthTest = false;
            grid.renderOrder = 1;
            object.add(grid);

            this.grid = grid;
            this.axes = axes;
            this.visible = false;
        }
        get visible() {
            return this._visible;
        }
        set visible(value) {
            this._visible = value;
            this.grid.visible = value;
            this.axes.visible = value;
        }
    }

    function makeAxisGrid(object, label, units) {
        const helper = new AxisGridHelper(object, units);
        gui.add(helper, 'visible').name(label);
    }

    makeAxisGrid(solarSystem, 'solarSystem', 25);
    makeAxisGrid(sunMesh, 'sunMesh');
    makeAxisGrid(earthSystem, 'earthSystem');
    makeAxisGrid(earthMesh, 'earthMesh');
    makeAxisGrid(moonSystem, 'moonSystem');
    makeAxisGrid(moonMesh, 'moonMesh');

    // 씬 가운데에 조명 배치
    {
        const color = 0xffffff;
        const intensity = 500;
        const light = new THREE.PointLight(color, intensity);
        scene.add(light);
    }

    // 카메라 : 
    const fov = 40;
    const aspect = 2;
    const near = 0.1;
    const far = 1000;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.set(0, 50, 0); // 카메라 위치
    //camera.up.set(0, 0, 1); // 카메라 위쪽 방향
    camera.lookAt(0, 0, 0); // 카메라가 바라보는 방향

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
        time *= 0.0005;

        if (resizeRendererToDisplaySize(renderer)) {
            const canvas = renderer.domElement;
            camera.aspect = canvas.clientWidth / canvas.clientHeight;
            camera.updateProjectionMatrix();
        }

        objects.forEach((object) => {
            object.rotation.y = time;
        })

        renderer.render(scene, camera);
        requestAnimationFrame(render);
    }

    requestAnimationFrame(render);
}
</script>
<style>
#canvas2 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>