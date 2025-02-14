<template>
	<div class="container">
		<div class="webgl-container" />
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

//let renderer, scene, camera, cube
const three = {
	renderer: null,
	scene: null,
	camera: null,
	cube: null,
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
	divContainer.value = document.querySelector(".webgl-container");

	// antialias : 오브젝트 경계선을 부드럽게 할 것이냐 말 것이냐.
	three.renderer = new THREE.WebGLRenderer({ antialias: true });
	three.renderer.setPixelRatio(window.devicePixelRatio);
	divContainer.value.appendChild(three.renderer.domElement);

	three.scene = new THREE.Scene();

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
	three.camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 100);
	three.camera.position.z = 2;
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
	const geometry = new THREE.BoxGeometry(1, 1, 1);
	const material = new THREE.MeshPhongMaterial({color: '#FFFFFF'});
	const cube = new THREE.Mesh(geometry, material);
	three.cube = cube;
	three.scene.add(three.cube);
}

/**
 * 창 크기 조절
 */
function resize() {
	const width = divContainer.value.clientWidth;
	const height = divContainer.value.clientHeight;
	
	three.camera.aspect = width / height;
	three.camera.updateProjectionMatrix();

	three.renderer.setSize(width, height);
}

/**
 * 
 * @param {Number} time 단위가 ms인 값값 
 */
function render(time) {
	console.log('기본 render');
	// camera 시점을 이용해서 scene을 렌더링해라.!
	three.renderer.render(three.scene, three.camera);
	update(time);
	// 애니메이션이 작동하는 코드. render 메서드를 계~속 호출. 
	animationFrameId = requestAnimationFrame(render);
}

function update(time) {
	time *= 0.001;
	three.cube.rotation.x = time;
	three.cube.rotation.y = time;
}
</script>

<style>
.container {
	width: 100%;
	height: 100vh;
}
.webgl-container {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%
}
</style>