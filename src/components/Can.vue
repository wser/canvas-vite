<script setup>
import { ref, onUpdated, onMounted } from 'vue'
import Sketch from 'sketch-js'

function parts(){

  // General Variables
  let particles = [];

  let sketch = Sketch.create();
  sketch.mouse.x = sketch.width / 2;
  sketch.mouse.y = sketch.height / 2;
  sketch.strokeStyle = 'hsla(200, 50%, 50%, .4)';
  sketch.globalCompositeOperation = 'lighter'; //'luminosity';

  // Particle Constructor
  let Particle = function () {
    this.x = random(sketch.width);
    this.y = random(sketch.height, sketch.height * 2);
    this.vx = 0;
    this.vy = -random(1, 10) / 5;
    this.radius = this.baseRadius = 1;
    this.maxRadius = 50;
    this.threshold = 150;
    this.hue = random(180, 240);
  };

  // Particle Prototype
  Particle.prototype = {
    update: function () {
      let radius;
      // Determine Distance From Mouse
      let distx = this.x - sketch.mouse.x;
      let disty = this.y - sketch.mouse.y;
      let dist = sqrt(distx * distx + disty * disty);

      // Set Radius
      if (dist < this.threshold) {
        radius =
          this.baseRadius +
          ((this.threshold - dist) / this.threshold) * this.maxRadius;
        this.radius = radius > this.maxRadius ? this.maxRadius : radius;
      } else {
        this.radius = this.baseRadius;
      }

      // Adjust Velocity
      this.vx += (random(100) - 50) / 1e3;
      this.vy -= random(1, 20) / 1e4;

      // Apply Velocity
      this.x += this.vx;
      this.y += this.vy;

      // Check Bounds
      if (
        this.x < -this.maxRadius ||
        this.x > sketch.width + this.maxRadius ||
        this.y < -this.maxRadius
      ) {
        this.x = random(sketch.width);
        this.y = random(sketch.height + this.maxRadius, sketch.height * 2);
        this.vx = 0;
        this.vy = -random(1, 10) / 5;
      }
    },
    render: function () {
      sketch.beginPath();
      sketch.arc(this.x, this.y, this.radius, 0, TWO_PI);
      sketch.closePath();
      // sketch.fillStyle = 'hsla(' + this.hue + ', 60%, 40%, .35)';
      // sketch.fill();
      sketch.stroke();
    },
  };

  // Create Particles
  let z = 500;
  while (z--) particles.push(new Particle());

  // Sketch Clear
  sketch.clear = () => sketch.clearRect(0, 0, sketch.width, sketch.height);

  // Sketch Update
  sketch.update = function () {
    let i = particles.length;
    let results = [];
    while (i--) results.push(particles[i].update());
    return results;
  };

  // Sketch Draw
  sketch.draw = function () {
    let i = particles.length;
    let results = [];
    while (i--) results.push(particles[i].render());
    return results;
  };

}

onMounted(() => parts())

onUpdated(() => parts())

</script>

