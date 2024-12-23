<template>
    <div>
        <h1>ref 핸들링</h1>
        <input type="text" ref="name" />
        <button @click="handleClick">클릭</button>
    </div>
</template>
<script setup>
import { createApp, ref } from 'vue'

const name = ref();
function handleClick() {
    name.value.classList.add('active');
    name.value.focus();
}

createApp({
    name: 'RefHandling',
})
</script>
