<template>
	<div>
		<h2>
			three 기본 강좌에 오신걸 환영합니다. 열심히 해보자!
		</h2>
		<el-tabs v-model="activeName" type="card">
			<el-tab-pane :label="tabs.threeBasic.label" :name="tabs.threeBasic.name">
				<ThreeBasic :active="tabs.threeBasic.name === activeName && active" />
			</el-tab-pane>
			<el-tab-pane :label="tabs.threeGeometry.label" :name="tabs.threeGeometry.name">
				<ThreeGeometry :active="tabs.threeGeometry.name === activeName && active" />
			</el-tab-pane>
			<el-tab-pane :label="tabs.threeScene.label" :name="tabs.threeScene.name">
				<ThreeScene :active="tabs.threeScene.name === activeName && active" />
			</el-tab-pane>
		</el-tabs>
	</div>
</template>

<script setup>
import { createApp, reactive, ref } from 'vue'
import ThreeBasic from '@/views/threeBasic/threeBasic.vue'
import ThreeGeometry from '@/views/threeBasic/threeGeometry.vue'
import ThreeScene from '@/views/threeBasic/threeScene.vue'

createApp({
	name: 'ThreeBasic',
	components: {
		ThreeBasic,
		ThreeGeometry,
		ThreeScene,
	}
})
const props = defineProps({
	active: {
		type: Boolean,
		required: true,
		default: false,
	}
})
const activeName = ref('threeBasic');
const tabs = reactive({
	threeBasic: {
		name: 'threeBasic',
		label: '기본',
	},
	threeGeometry: {
		name: 'threeGeometry',
		label: '지오메트리',
	},
	threeScene: {
		name: 'threeScene',
		label: '씬 혹은 장면',
	}
})
</script>

<style>

</style>
