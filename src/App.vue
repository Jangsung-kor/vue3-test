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
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background-color: #E2E2E2;
}

</style>
