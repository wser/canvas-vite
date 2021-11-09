<script setup>
/** @type {HTMLCanvasElement} */
import { ref, reactive, computed, watch, onUpdated, onMounted } from 'vue'

let canvasRef = ref(null)

let size = { w: 350, h: 550 }

let setCanvas = function() {  
  this.w = size.w;
  this.h = size.h;
  this.ctx = null;  
  this.setContext = ctx => this.ctx = ctx;  
}

let canv = new setCanvas()

let drawLine = (w, h, ctx) => {
  ctx.beginPath();
  ctx.moveTo(0, 0);
  ctx.lineTo(w, h);
  ctx.closePath();
  ctx.stroke();    
}

let draw = () => {
  canv.setContext(canvasRef.value.getContext('2d'))
  drawLine(size.w, size.h, canv.ctx)
}

onMounted(() => draw())

onUpdated(() => draw())

</script>

<template>
  <canvas 
    ref='canvasRef' 
    id='canvas1' 
    :width="size.w" 
    :height="size.h" 
    style="border:1px solid #000000;"
  ></canvas>
</template>

<style scoped>
#canvas1 {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
</style>
