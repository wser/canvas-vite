<script setup>
/** @type {HTMLCanvasElement} */
import { ref, reactive, computed, watch, onUpdated, onMounted } from 'vue'

import Sketch from 'sketch-js'

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
  // drawLine(size.w, size.h, canv.ctx)
  balloonz()
}

let balloonz = () => {
        
  let sketch = Sketch.create();

  let balloons = [];

  let options = {
      amount: 5,
      sizeMin: 5,
      sizeMax: 10
  };

  let sortBySize = function(a, b) {
      if (a.size < b.size) return 1;
      if (a.size > b.size) return -1;
      return 0;
  };

  let Balloon = function(config) {
      this.x = config.x;
      this.y = config.y;
      this.vx = 0;
      this.vy = 0;
      this.size = config.size;
      this.update = function() {
        var dt;
        dt = sketch.dt <= 0 ? .001 : sketch.dt / 16;
        this.vx += this.size * (random(-1, 1) / 1000);
        this.x += this.vx * dt;
        this.vy -= this.size / 2000;
        this.y += this.vy * dt;
        if (this.y <= this.size * -0.9) {
            this.size = random(options.sizeMin, options.sizeMax);
            this.x = random(sketch.width);
            this.vx = 0;
            this.y = sketch.height + (this.size * 10.2);
            this.vy = 0;
        }
      };
      this.renderBalloon = function() {
        sketch.save();
        sketch.translate(this.x, this.y);
        sketch.beginPath();
        sketch.moveTo(this.size * -0.5, 0);
        sketch.bezierCurveTo(this.size * -5, this.size * -1, this.size * -6, this.size * -10, 0, this.size * -10);
        sketch.bezierCurveTo(this.size * 6, this.size * -10, this.size * 5, this.size * -1, this.size * 0.5, 0);
        sketch.lineTo(this.size * 0.8, this.size * 0.7);
        sketch.lineTo(this.size * -0.8, this.size * 0.7);
        sketch.closePath();
        sketch.fillStyle = 'hsla( 0, 0%, ' + (0 + ((this.size / options.sizeMax) * 100)) + '%, .35 )';
        sketch.fill();
        sketch.lineWidth = this.size * 0.4;
        sketch.stroke();
        sketch.restore();
        sketch.save();
        sketch.translate(this.x - (this.size * 1.75), this.y - (this.size * 7.5));
        sketch.rotate(PI / 4);
        sketch.scale(1, 2);
        sketch.beginPath();
        sketch.arc(0, 0, this.size * 0.5, 0, TWO_PI);
        sketch.fillStyle = 'hsla( 0, 0%, 100%, .8 )';
        sketch.fill();
        sketch.restore();
      };
  };

  sketch.setup = function() {
      sketch.lineJoin = 'round';
      sketch.strokeStyle = 'hsla( 0, 0%, 100%, .75 )';
      let i = options.amount;
      let _results = [];
      while (i--) {
          _results.push(balloons.push(new Balloon({
              x: random(sketch.width),
              y: random(sketch.height),
              size: random(options.sizeMin, options.sizeMax)
          })));
      }
      return _results;
  };

  sketch.clear = function() {
      return sketch.clearRect(0, 0, sketch.width, sketch.height);
  };

  sketch.update = function() {
      balloons.sort(sortBySize);
      let i = balloons.length;
      let _results = [];
      while (i--) { _results.push(balloons[i].update(i)); }
      return _results;
  };

  sketch.draw = function() {
      let i = balloons.length;
      let _results = [];
      while (i--) { _results.push(balloons[i].renderBalloon(i)); }
      return _results;
  };

  // sketch.keydown = function(){
  //   if ( this.keys.C ) sketch.clear();
  // }

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
