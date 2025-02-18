<template>
	<div class="container">
		<div id="webgl-container3" />
	</div>
</template>

<script setup>
import { watch, ref } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

const props = defineProps({
	active: {
		type: Boolean,
		required: true,
		default: false,
	}
})

const three = {
    renderer: null,
    scene: null,
    camera: null,
    cube: null,
    solarSystem: null,
    earthOrbit: null,
    moonOrbit: null,
}
let animationFrameId = null;
const divContainer = ref(null);

watch(() => props.active, (active) => {
	if (active) {
		init();
	} else {
        cancelAnimationFrame(animationFrameId);
        animationFrameId = null;
    }
})

function init() {
	divContainer.value = document.querySelector("#webgl-container3");

	// antialias : 오브젝트 경계선을 부드럽게 할 것이냐 말 것이냐.
	three.renderer = new THREE.WebGLRenderer({ antialias: true });
	three.renderer.setPixelRatio(window.devicePixelRatio);
	divContainer.value.appendChild(three.renderer.domElement);

	three.scene = new THREE.Scene();

	setupCamera();
	setupLight();
	setupModel();
    setupControls();

	window.onresize = resize;
	resize();

	requestAnimationFrame(render);
}

/**
 * 카메라 세팅
 */
function setupCamera() {
	const width = divContainer.value.clientWidth;
	const height = divContainer.value.clientHeight;
	three.camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 100);
	three.camera.position.z = 20;
}

/**
 * 조명 세팅
 */
function setupLight() {
	const color = 0xffffff;
	const intensity = 1;
	const light = new THREE.DirectionalLight(color, intensity);
	light.position.set(-1, 2, 4);
	three.scene.add(light);
}

/**
 * 형상 세팅
 */
function setupModel() {
    const solarSystem = new THREE.Object3D();
    three.scene.add(solarSystem);

    // // 태양 크기
    const radius = 1;
    const widthSegments = 12;
    const heightSegments = 12;
    const sphereGeometry = new THREE.SphereGeometry(radius, widthSegments, heightSegments);

    const sunMaterial = new THREE.MeshPhongMaterial({
        emissive: 0xffff00, flatShading: true
    });

    const sunMesh = new THREE.Mesh(sphereGeometry, sunMaterial);
    sunMesh.scale.set(3, 3, 3);
    solarSystem.add(sunMesh);

    const earthOrbit = new THREE.Object3D();
    solarSystem.add(earthOrbit);
    earthOrbit.position.x = 10; // 태양에서 10만큼 떨어진 곳곳

    const earthMaterial = new THREE.MeshPhongMaterial({
        color: 0x2233ff, emissive: 0x112244, flatShading: true
    });

    const earthMesh = new THREE.Mesh(sphereGeometry, earthMaterial);
    earthOrbit.add(earthMesh);

    const moonOrbit = new THREE.Object3D();
    earthOrbit.add(moonOrbit);
    moonOrbit.position.x = 3;

    const moonMaterial = new THREE.MeshPhongMaterial({
        color: 0x888888, emissive: 0x222222, flatShading: true
    });

    const moonMesh = new THREE.Mesh(sphereGeometry, moonMaterial);
    moonMesh.scale.set(0.3, 0.3, 0.3);
    moonOrbit.add(moonMesh);

    three.solarSystem = solarSystem;
    three.earthOrbit = earthOrbit;
    three.moonOrbit = moonOrbit;
}

function setupControls() {
    new OrbitControls(three.camera, divContainer.value);
}

/**
 * 창 크기 조절
 */
function resize() {
	const width = divContainer.value.clientWidth;
	const height = divContainer.value.clientHeight;
	
	three.camera.aspect = width / height;
	three.camera.updateProjectionMatrix();

	three.renderer.setSize(width, height, false);
}

/**
 * 
 * @param {Number} time 단위가 ms인 값값 
 */
function render(time) {
    console.log('씬 Sceme');
	// camera 시점을 이용해서 scene을 렌더링해라.!
	three.renderer.render(three.scene, three.camera);
	update(time);
	// 애니메이션이 작동하는 코드. render 메서드를 계~속 호출. 
	animationFrameId = requestAnimationFrame(render);
}

function update(time) {
	time *= 0.001;
	
    three.solarSystem.rotation.y = time / 2;
    three.earthOrbit.rotation.y = time * 2;
    three.moonOrbit.rotation.y = time * 5;
}
</script>

<style>
.container {
	width: 100%;
	height: 100vh;
}
#webgl-container3 {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%
}
</style>