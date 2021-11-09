<script setup>
function CanvasManager(w, h) {
  this.w = w;
  this.h = h;
  this.ctx = null;
  
  this.setContext = function(ctx) {
  	this.ctx = ctx;
  }
  this.setSize = function(size) {
  	this.w += size.w;
    this.h += size.h;
  }
  this.drawGrid = function() {
  	const ctx = this.ctx;
		ctx.beginPath();
    ctx.moveTo(0, 0);
    ctx.lineTo(this.w, this.h);
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
  <canvas 
    ref='myCanvas' 
    id='canvas1' 
    :width="size.w" 
    :height="size.h" 
    style="border:1px solid #000000;"
  ></canvas>
  </template>

<style scoped>
/* #canvas1 {
    width: 90vw; 
    height: 90vh; 
    border: 1px solid green;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  } */
</style>
