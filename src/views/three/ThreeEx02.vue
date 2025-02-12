<template>
	<div>
		<canvas id="canvas11" />
	</div>
</template>
<script setup>
import { createApp, watch } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

createApp({
	name: 'ThreeEx02',
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
	const canvas = document.querySelector('#canvas11');
	const renderer = new THREE.WebGLRenderer({
		antialias: true,
		canvas,
	})
}
</script>