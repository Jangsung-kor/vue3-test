<template>
    <div class="body">
        <canvas id="canvas9" />
        <div class="split">
            <div id="view1" tabindex="1" />
            <div id="view2" tabindex="2" />
        </div>
    </div>
</template>
<script setup>
/*
렌더 타겟(Render Targets)이란, 직접 렌더링할 수 있는 텍스처(texture)다. (텍스처는 질감이다.)
즉, 내가 만든 장면(Scene)을 어느 한 형상의 텍스처(질감)로 사용할 수 있다.
예)
    그림자,
    피킹(picking) : 클릭이나 터치한 물체를 가려내는 작업,
    후처리 효과
*/
import { createApp, watch } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

createApp({
    name: 'RenderTargets',
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
    // 캔버스와 렌더
    const canvas = document.querySelector('#canvas9');
    const view1Elem = document.querySelector('#view1');
    const view2Elem = document.querySelector('#view2');
    const renderer = new THREE.WebGLRenderer({
        antialias: true,
        canvas,
    })

    // WebGLRenderTarget 인스턴스 생성
    const rtWidth = 512;
    const rtHeight = 512;
    const renderTarget = new THREE.WebGLRenderTarget(rtWidth, rtHeight);

    // 카메라와 장면 추가
    const rtFov = 75;
    const rtAspect = rtWidth / rtHeight;
    const rtNear = 0.1;
    const rtFar = 5;
    const rtCamera = new THREE.PerspectiveCamera(rtFov, rtAspect, rtNear, rtFar);
    rtCamera.position.z = 2;

    const rtScene = new THREE.Scene();
    rtScene.background = new THREE.Color('lightbrown');
    // 조명 추가
    {
        const color = 0xFFFFFF;
        const intensity = 1;
        const light = new THREE.DirectionalLight(color, intensity);
        light.position.set(-1, 2, 4);
        rtScene.add(light);
    }

    // 정육면체 3개 추가
    const boxWidth = 1;
    const boxHeight = 1;
    const boxDepth = 1;
    const geometry = new THREE.BoxGeometry(boxWidth, boxHeight, boxDepth);

    function makeInstance(geometry, color, x) {
        const material = new THREE.MeshPhongMaterial({ color });
        const cube = new THREE.Mesh(geometry, material);
        rtScene.add(cube);
        cube.position.x = x;
        return cube;
    }

    const rtCubes = [
        makeInstance(geometry, 0x44aa88, 0),
        makeInstance(geometry, 0x8844aa, -2),
        makeInstance(geometry, 0xaa8844, 2),
    ]

    // 기본 카메라와 장면
    const fov = 75;
    const aspect = 2;
    const near = 0.1;
    const far = 100;
    const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
    camera.position.set( 0, 10, 20 );
    //camera.position.z = 2;

    const cameraHelper = new THREE.CameraHelper(camera);

    const camera2 = new THREE.PerspectiveCamera(60, 2, 0.1, 500);
    camera2.position.set(40, 10, 30);
    camera2.lookAt(0, 5, 0);

    const scene = new THREE.Scene();
    scene.background = new THREE.Color('black');
    scene.add(cameraHelper);

    const controls = new OrbitControls(camera, view1Elem);
    controls.target.set(0, 0, 0);
    controls.update();

    const controls2 = new OrbitControls(camera2, view2Elem);
    controls2.target.set(0, 5, 0);
    controls2.update();

    // 조명
    {
        const color = 0xFFFFFF;
        const intensity = 1;
        const light = new THREE.DirectionalLight(color, intensity);
        const lightHelper = new THREE.DirectionalLightHelper(light);
        light.position.set(-1, 2, 4);
        scene.add(light);
        scene.add(lightHelper);
    }

    const material = new THREE.MeshPhongMaterial({
        map: renderTarget.texture,
    });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);

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

    // 요소를 자르는 가위
    function setScissorForElement(elem) {
        const canvasRect = canvas.getBoundingClientRect();
        const elemRect = elem.getBoundingClientRect();

		// compute a canvas relative rectangle
		const right = Math.min( elemRect.right, canvasRect.right ) - canvasRect.left;
		const left = Math.max( 0, elemRect.left - canvasRect.left );
		const bottom = Math.min( elemRect.bottom, canvasRect.bottom ) - canvasRect.top;
		const top = Math.max( 0, elemRect.top - canvasRect.top );

		const width = Math.min( canvasRect.width, right - left );
		const height = Math.min( canvasRect.height, bottom - top );

		// setup the scissor to only render to that part of the canvas
		const positiveYUpBottom = canvasRect.height - bottom;
		renderer.setScissor( left, positiveYUpBottom, width, height );
		renderer.setViewport( left, positiveYUpBottom, width, height );

		// return the aspect
		return width / height;        
    }

    function render(time) {
        time *= 0.001;

        if (resizeRendererToDisplaySize(renderer)) {
            const canvas = renderer.domElement;
            camera.aspect = canvas.clientWidth / canvas.clientHeight;
            camera.updateProjectionMatrix();

            // 렌더 타겟 크기 조정
            renderTarget.setSize(canvas.width, canvas.height);
            rtCamera.aspect = camera.aspect;
            rtCamera.updateProjectionMatrix();
        }
        // 렌더 타겟 장면 안에서 정육면체를 회전
        rtCubes.forEach((cube, ndx) => {
            const speed = 1 + ndx * 0.1;
            const rot = time * speed;
            cube.rotation.x = rot;
            cube.rotation.y = rot;
        });

        // 렌더 타겟 장면을 렌더 타겟에 렌더링
        renderer.setRenderTarget(renderTarget);
        renderer.render(rtScene, rtCamera);
        renderer.setRenderTarget(null);

        cube.rotation.x = time;
        cube.rotation.y = time * 1.1;

        // 첫 번째 카메라
        {
            const aspect = setScissorForElement(view1Elem);

            camera.aspect = aspect;
            camera.updateProjectionMatrix();
            cameraHelper.update();

            cameraHelper.visible = false;

            scene.background.set(0x000000);

            renderer.render(scene, camera);
        }

        // 두 번째 카메라
        {
            const aspect = setScissorForElement(view2Elem);

            camera2.aspect = aspect;
            camera2.updateProjectionMatrix();
            
            cameraHelper.visible = true;

            scene.background.set(0x000040);

            renderer.render(scene, camera2);
        }
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

#canvas9 {
    width: 100%;
    height: 100%;
    display: block;
}

.split {
    position: absolute;
    top: 0;
    width: 100%;
    height: 100%;
    display: flex;
}
.split > div {
    width: 100%;
    height: 100%;
}
</style>