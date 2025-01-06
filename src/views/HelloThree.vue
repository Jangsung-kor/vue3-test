<template>
    <h1>three.js에 오신 걸 환영합니다.</h1>
    <div>
        <el-tabs v-model="activeName">
            <el-tab-pane type="card" :label="tabs.ex01.label" :name="tabs.ex01.name">
                <ThreeEx01 :activeTab="tabs.ex01.name === activeName && active"/>
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.primitives.label" :name="tabs.primitives.name">
                <Primitives :activeTab="tabs.primitives.name === activeName && active"/>
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.sceneGraph.label" :name="tabs.sceneGraph.name">
                <SceneGraph :activeTab="tabs.sceneGraph.name === activeName && active"/>
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.realSolarSystem.label" :name="tabs.realSolarSystem.name"> 
                <RealSolarSystem :activeTab="tabs.realSolarSystem.name === activeName && active" />
            </el-tab-pane>
            <el-tab-pane type="card" :label="tabs.threeTexture.label" :name="tabs.threeTexture.name">
                <ThreeTexture :activeTab="tabs.threeTexture.name === activeName && active" />
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

createApp({
    name: 'HelloThree',
    components: {
        ThreeEx01,
        Primitives,
        SceneGraph,
        RealSolarSystem,
        ThreeTexture,
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
    }
})
</script>