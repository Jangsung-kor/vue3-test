<template>
  <div>
    <canvas id="canvas3" />
  </div>
</template>
<script setup type="module">
import { createApp, watch, defineProps } from 'vue'
import * as THREE from 'three'

createApp({
  name: 'realSolarSystem',
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
  const canvas = document.querySelector('#canvas3');
  const renderer = new THREE.WebGLRenderer({ antialias: true, canvas});
  const scene = new THREE.Scene();

  // 구 생성
  const radius = 1;
  const widthSegments = 6; // 적도를 중심으로 36분할
  const heightSegments = 6;
  const sphereGeometry = new THREE.SphereGeometry(radius, widthSegments, heightSegments);

  // 태양계 생성
  const solarSystem = new THREE.Object3D();
  scene.add(solarSystem);

  // 태양 생성
  const sunMaterial = new THREE.MeshPhongMaterial({ emissive: 0xffff00});
  const sunMesh = new THREE.Mesh(sphereGeometry, sunMaterial);
  sunMesh.scale.set(3, 3, 3);
  solarSystem.add(sunMesh);
  
  // 지구 궤도 생성
  const earthOrbit = new THREE.Object3D();
  earthOrbit.position.x = 9;
  solarSystem.add(earthOrbit);

  // 지구 생성
  const earthMaterial = new THREE.MeshPhongMaterial({ color: 0x2233ff, emissive: 0x112244});
  const earthMesh = new THREE.Mesh(sphereGeometry, earthMaterial);
  earthOrbit.add(earthMesh);

  // 달 생성
  const moonOrbit = new THREE.Object3D();
  moonOrbit.position.x = 3;
  earthOrbit.add(moonOrbit);

  const moonMaterial = new THREE.MeshPhongMaterial({ color: 0x888888, emissive: 0x222222});
  const moonMesh = new THREE.Mesh(sphereGeometry, moonMaterial);
  moonMesh.scale.set(0.5, 0.5, 0.5);
  moonOrbit.add(moonMesh);

  // 태양 빛
  const light = new THREE.PointLight(0xffffff, 1000);
  scene.add(light);

  // 카메라
  const fov = 40;
  const aspect = 2;
  const near = 0.1;
  const far = 1000;
  const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
  camera.position.set(0, 60, 0);
  camera.lookAt(0, 0, 0);

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
    time *= 0.01;

    if (resizeRendererToDisplaySize(renderer)) {
            const canvas = renderer.domElement;
            camera.aspect = canvas.clientWidth / canvas.clientHeight;
            camera.updateProjectionMatrix();
    }

    solarSystem.rotation.y = time / 365; // 지구 공전
    sunMesh.rotation.y = time / 27; // 태양 자전
    earthOrbit.rotation.y = 0;  // 달 공전
    earthMesh.rotation.y = time; // 지구 자전
    moonMesh.rotation.y = time / 27; // 달 자전
    
    renderer.render(scene, camera);
    requestAnimationFrame(render);
  }

  requestAnimationFrame(render);

}
</script>
<style>
#canvas3 {
    width: 100%;
    height: 100%;
    display: block;
}
</style>