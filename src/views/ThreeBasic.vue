<template>
	<div class="container">
		<h2>
			three 기본 강좌에 오신걸 환영합니다. 열심히 해보자!
		</h2>
		<div id="webgl-container" />
	</div>
</template>

<script setup>
import { watch, ref } from 'vue'
import * as THREE from 'three'

const props = defineProps({
	active: {
		type: Boolean,
		required: true,
		default: false,
	}
})

let renderer, scene, camera, cube
const divContainer = ref(null);

watch(() => props.active, (active) => {
	if (active) {
		init();
	}
})

function init() {
	divContainer.value = document.querySelector("#webgl-container");

	// antialias : 오브젝트 경계선을 부드럽게 할 것이냐 말 것이냐.
	renderer = new THREE.WebGLRenderer({ antialias: true });
	renderer.setPixelRatio(window.devicePixelRatio);
	divContainer.value.appendChild(renderer.domElement);

	scene = new THREE.Scene();

	setupCamera();
	setupLight();
	setupModel();

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
	camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 100);
	camera.position.z = 2;
}

/**
 * 조명 세팅
 */
function setupLight() {
	const color = 0xffffff;
	const intensity = 1;
	const light = new THREE.DirectionalLight(color, intensity);
	light.position.set(-1, 2, 4);
	scene.add(light);
}

/**
 * 형상 세팅
 */
function setupModel() {
	const geometry = new THREE.BoxGeometry(1, 1, 1);
	const material = new THREE.MeshPhongMaterial({color: 0x44a88});
	cube = new THREE.Mesh(geometry, material);
	scene.add(cube);
}

/**
 * 창 크기 조절
 */
function resize() {
	const width = divContainer.value.clientWidth;
	const height = divContainer.value.clientHeight;
	
	camera.aspect = width / height;
	camera.updateProjectionMatrix();

	renderer.setSize(width, height);
}

/**
 * 
 * @param {Number} time 단위가 ms인 값값 
 */
function render(time) {
	// camera 시점을 이용해서 scene을 렌더링해라.!
	renderer.render(scene, camera);
	update(time);
	// 애니메이션이 작동하는 코드. render 메서드를 계~속 호출. 
	requestAnimationFrame(render);
}

function update(time) {
	time *= 0.001;
	cube.rotation.x = time;
	cube.rotation.y = time;
}
</script>

<style>
.container {
	width: 100%;
	height: 100vh;
}
#webgl-container {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%
}
</style>