<template>
    <h1>three.js에 오신 걸 환영합니다.</h1>
    <div>
        <el-tabs v-model="activeName">
            <el-tab-pane type="card" :label="tabs.ex01.label" :name="tabs.ex01.name">
                <ThreeEx01 :activeTab="tabs.ex01.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.primitives.label" :name="tabs.primitives.name">
                <Primitives :activeTab="tabs.primitives.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.sceneGraph.label" :name="tabs.sceneGraph.name">
                <SceneGraph :activeTab="tabs.sceneGraph.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.realSolarSystem.label" :name="tabs.realSolarSystem.name">
                <RealSolarSystem :activeTab="tabs.realSolarSystem.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.threeTexture.label" :name="tabs.threeTexture.name">
                <ThreeTexture :activeTab="tabs.threeTexture.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.threeLight.label" :name="tabs.threeLight.name">
                <ThreeLight :activeTab="tabs.threeLight.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.threeCamera.label" :name="tabs.threeCamera.name">
                <ThreeCamera :activeTab="tabs.threeCamera.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.threeShadows.label" :name="tabs.threeShadows.name">
                <ThreeShadows :activeTab="tabs.threeShadows.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.threeFog.label" :name="tabs.threeFog.name">
                <ThreeFog :activeTab="tabs.threeFog.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.renderTargets.label" :name="tabs.renderTargets.name">
                <RenderTargets :activeTab="tabs.renderTargets.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.bufferGeometry.label" :name="tabs.bufferGeometry.name">
                <BufferGeometry :activeTab="tabs.bufferGeometry.name === activeName && active" />
            </el-tab-pane>
        </el-tabs>
    </div>
</template>
<script setup type="module">
import { createApp, reactive, ref } from 'vue'
import ThreeEx01 from '@/views/three/ThreeEx01.vue'
import Primitives from '@/views/three/Primitives.vue'
import SceneGraph from '@/views/three/SceneGraph.vue'
import RealSolarSystem from '@/views/three/RealSolarSystem.vue';
import ThreeTexture from '@/views/three/ThreeTexture.vue';
import ThreeLight from '@/views/three/ThreeLight.vue';
import ThreeCamera from '@/views/three/ThreeCamera.vue';
import ThreeShadows from '@/views/three/ThreeShadows.vue';
import ThreeFog from '@/views/three/ThreeFog.vue';
import RenderTargets from '@/views/three/RenderTargets.vue';
import BufferGeometry from '@/views/three/BufferGeometry.vue';

createApp({
    name: 'HelloThree',
    components: {
        ThreeEx01,
        Primitives,
        SceneGraph,
        RealSolarSystem,
        ThreeTexture,
        ThreeLight,
        ThreeCamera,
        ThreeShadows,
        ThreeFog,
        RenderTargets,
        BufferGeometry,
    }
})

const props = defineProps({
    active: {
        type: Boolean,
        required: true,
        default: false,
    }
})

const activeName = ref('ex01');
const tabs = reactive({
    ex01: {
        name: 'ex01',
        label: 'ex01',
    },
    primitives: {
        name: 'primitives',
        label: 'primitives',
    },
    sceneGraph: {
        name: 'sceneGraph',
        label: 'sceneGraph',
    },
    realSolarSystem: {
        name: 'realSolarSystem',
        label: 'realSolarSystem'
    },
    threeTexture: {
        name: 'threeTexture',
        label: 'threeTexture',
    },
    threeLight: {
        name: 'threeLight',
        label: 'threeLight',
    },
    threeCamera: {
        name: 'threeCamera',
        label: 'threeCamera',
    },
    threeShadows: {
        name: 'threeShadows',
        label: 'threeShadows',
    },
    threeFog: {
        name: 'threeFog',
        label: 'threeFog',
    },
    renderTargets: {
        name: 'renderTargets',
        label: 'renderTargets',
    },
    bufferGeometry: {
        name: 'bufferGeometry',
        label: 'bufferGeometry',
    }
})
</script>