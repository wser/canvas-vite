<script setup>
function CanvasManager(w, h) {
  let t = this;
  t.w = w;
  t.h = h;
  t.ctx = null;
  
  t.setContext = function(ctx) {
    t.ctx = ctx;
  }
  t.setSize = function(size) {
    t.w += size.w;
    t.h += size.h;
  }
  t.drawGrid = function() {
    const ctx = t.ctx;
		ctx.beginPath();
    ctx.moveTo(0, 0);
    ctx.lineTo(t.w, t.h);
    ctx.stroke();
  }
}

/***** APP *****/
import {  ref, reactive, computed, watch, onUpdated, onMounted } from 'vue'



let myCanvas = ref(null)
let size = reactive({
  w: 200,
  h: 200
})

let manager = new CanvasManager(size.w, size.h)

function grow() {
  size.w += 10
  size.h += 10
  manager.setSize(size)
}

function draw() {
  manager.drawGrid(4, 4, 1, 'black')
}

onMounted(() => {
  manager.setContext(myCanvas.value.getContext('2d'))
  draw()
})

onUpdated(() => draw())

</script>




<template>


<button @click="grow">Grow</button>
  <br><br>
  <div class="board">
    <canvas ref='myCanvas' :width="size.w" :height="size.h" tabindex='0' 
            style="border:1px solid #000000;"
            ></canvas>
  </div>

</template>

<style scoped></style>
