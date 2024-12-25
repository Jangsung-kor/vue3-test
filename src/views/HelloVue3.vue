<template>
  <div class="hello">
    <h1>{{ msg1 }}</h1>
    <el-tabs v-model="activeName">
      <el-tab-pane type="card" :label="tabs.mouseEvent.label" :name="tabs.mouseEvent.name">
        <!-- 마우스 이벤트 -->
        <MouseEvent />
      </el-tab-pane>
      <el-tab-pane type="card" :label="tabs.refHandling.label" :name="tabs.refHandling.name">
        <!-- ref 핸들링 -->
        <RefHandling />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script setup>
import { createApp, ref, reactive } from 'vue'
import MouseEvent from '@/views/vue3/MouseEvent.vue'
import RefHandling from '@/views/vue3/RefHandling.vue'
const tabs = reactive({
  mouseEvent: {
    name: 'mouseEvent',
    label: '마우스 이벤트',
  },
  refHandling: {
    name: 'refHandling',
    label: 'ref 핸들링',
  }
})
const activeName = ref('mouseEvent');
const msg1 = ref('vue3에 오신 걸 환영합니다.');

createApp({
  name: 'HelloVue3',
  components: {
    MouseEvent,
    RefHandling,
  }
})

</script>
