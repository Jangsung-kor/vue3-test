<template>
  <MainIndex />
</template>

<script setup>
import { createApp } from 'vue'
import MainIndex from '@/views'

createApp({
  name: 'App',
  components: {
    MainIndex,
  }
})
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

#app {
  width: 100vw;
  height: 100vh;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #000000;
  background-color: #E2E2E2;
}
</style>
