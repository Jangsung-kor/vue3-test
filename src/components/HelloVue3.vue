<template>
  <div class="hello">
    <h1>{{ msg1 }}</h1>
  </div>
</template>

<script setup>
import { createApp, ref } from 'vue'

const msg1 = ref('vue3에 오신 걸 환영합니다.')
createApp({
  name: 'HelloVue3',
})
</script>


