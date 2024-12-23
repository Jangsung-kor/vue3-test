<template>
    <div>
        <h1>마우스 이벤트</h1>
        <div class="box" @mouseover="handleEvent($event, 5)">
            mouseover (enter)
        </div>
        <div class="box" @mouseleave="handleEvent">
            mouseleave
        </div>
        <div class="box" @dblclick="handleEvent">
            doublic click
        </div>
        <div class="box" @mousemove="handleMousemove">
            position - {{ x }}, {{ y }}
        </div>
    </div>
</template>

<script setup>
import { createApp, ref } from 'vue'

const x = ref(0);
const y = ref(0);

function handleEvent(e) {
  console.log(e, e.type);
}
function handleMousemove(e) {
  x.value = e.offsetX;
  y.value = e.offsetY;
}

createApp({
    name: 'MouseEvent',
})
</script>
<style>
.box {
  width: 100px;
  background: #ddd;
  margin: 20px;
  padding: 100px 0;
  text-align: center;
  display: inline-block;
}
</style>