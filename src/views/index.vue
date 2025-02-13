<template>
    <el-tabs v-model="activeName">
        <el-tab-pane :label="tabs.vue3.label" :name="tabs.vue3.name">
            <img alt="Vue logo" src="@/assets/logo.png">
            <HelloVue3 msg="vue3에 오신 걸 환영합니다." />
        </el-tab-pane>
        <el-tab-pane :label="tabs.three.label" :name="tabs.three.name">
            <HelloThree :active="tabs.three.name === activeName" />
        </el-tab-pane>
        <el-tab-pane :label="tabs.threeBasic.label" :name="tabs.threeBasic.name">
            <ThreeBasic :active="tabs.threeBasic.name === activeName" />
        </el-tab-pane>
    </el-tabs>
</template>
<script setup>
import { ref, reactive, createApp } from 'vue'
import HelloVue3 from '@/views/HelloVue3.vue'
import HelloThree from '@/views/HelloThree.vue'
import ThreeBasic from '@/views/ThreeBasic.vue'

createApp({
    name: 'mainIndex',
    components: {
        HelloVue3,
        HelloThree,
        ThreeBasic,
    }
})

const activeName = ref('vue3')
const tabs = reactive({
    vue3: {
        name: 'vue3',
        label: 'vue3',
    },
    three: {
        name: 'three',
        label: 'three',
    },
    threeBasic: {
        name: 'threeBasic',
        label: 'three 기본',
    },
})
</script>