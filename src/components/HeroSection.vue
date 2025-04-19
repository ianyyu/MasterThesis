<template>
  <section class="hero-container">
    <div class="hero-text">
      Public understanding of AI's energy cost is still in its infancy. Many users remain unaware that an AI query "costs" anything beyond their internet connection. This project is an exploration of energy consumption of AI.
    </div>
    <div class="footer">
      <div class="footer-left">
        <button class="footer-button">In The Age of AI</button>
      </div>
      <div class="footer-center">
        <span class="scroll-text">(Scroll down)</span>
      </div>
      <div class="footer-right">
        <button class="footer-button">Thesis</button>
        <button class="footer-button">More</button>
      </div>
    </div>
    <div class="content--canvas"></div>
  </section>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { seed, simplex2 } from '../utils/noise';

// Animation constants
const PARTICLE_COUNT = 700;
const RANGE_Y = 100;
const BASE_TTL = 50;
const RANGE_TTL = 150;
const BASE_SPEED = 0.1;
const RANGE_SPEED = 2;
const BASE_RADIUS = 1;
const RANGE_RADIUS = 4;
const BASE_HUE = 220;
const RANGE_HUE = 100;
const NOISE_STEPS = 8;
const X_OFF = 0.00125;
const Y_OFF = 0.00125;
const Z_OFF = 0.0005;
const BACKGROUND_COLOR = 'hsla(260,40%,5%,1)';
const TAU = Math.PI * 2;

let container;
let canvasA;
let canvasB;
let ctxA;
let ctxB;
let center;
let tick = 0;
let particles = [];

class Particle {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.vx = 0;
    this.vy = 0;
    this.life = 0;
    this.ttl = BASE_TTL + Math.random() * RANGE_TTL;
    this.speed = BASE_SPEED + Math.random() * RANGE_SPEED;
    this.radius = BASE_RADIUS + Math.random() * RANGE_RADIUS;
    this.hue = BASE_HUE + Math.random() * RANGE_HUE;
  }

  update() {
    const noise = simplex2(
      this.x * X_OFF, 
      this.y * Y_OFF + tick * Z_OFF
    ) * NOISE_STEPS * TAU;
    
    this.vx = lerp(this.vx, Math.cos(noise), 0.5);
    this.vy = lerp(this.vy, Math.sin(noise), 0.5);
    
    const x2 = this.x + this.vx * this.speed;
    const y2 = this.y + this.vy * this.speed;
    
    this.draw(x2, y2);
    
    this.x = x2;
    this.y = y2;
    this.life++;
    
    if (this.checkBounds() || this.life > this.ttl) {
      this.reset();
    }
  }
  
  draw(x2, y2) {
    ctxA.save();
    ctxA.lineCap = 'round';
    ctxA.lineWidth = this.radius;
    ctxA.strokeStyle = `hsla(${this.hue},100%,60%,${fadeInOut(this.life, this.ttl)})`;
    ctxA.beginPath();
    ctxA.moveTo(this.x, this.y);
    ctxA.lineTo(x2, y2);
    ctxA.stroke();
    ctxA.closePath();
    ctxA.restore();
  }
  
  checkBounds() {
    return (
      this.x > canvasA.width ||
      this.x < 0 ||
      this.y > canvasA.height ||
      this.y < 0
    );
  }
  
  reset() {
    this.x = Math.random() * canvasA.width;
    this.y = center[1] + (Math.random() * 2 - 1) * RANGE_Y;
    this.life = 0;
  }
}

function lerp(start, end, amt) {
  return (1 - amt) * start + amt * end;
}

function fadeInOut(t, m) {
  let hm = 0.5 * m;
  return Math.abs(((t + hm) % m) - hm) / hm;
}

const initCanvas = () => {
  container = document.querySelector('.content--canvas');
  canvasA = document.createElement('canvas');
  canvasB = document.createElement('canvas');
  
  canvasB.style = `
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  `;
  
  container.appendChild(canvasB);
  
  ctxA = canvasA.getContext('2d');
  ctxB = canvasB.getContext('2d');
  center = [0, 0];
  
  const resize = () => {
    const { innerWidth, innerHeight } = window;
    
    canvasA.width = innerWidth;
    canvasA.height = innerHeight;
    
    ctxA.drawImage(canvasB, 0, 0);
    
    canvasB.width = innerWidth;
    canvasB.height = innerHeight;
    
    ctxB.drawImage(canvasA, 0, 0);
    
    center[0] = 0.5 * canvasA.width;
    center[1] = 0.5 * canvasA.height;
    
    initParticles();
  };
  
  window.addEventListener('resize', resize);
  resize();
  
  return () => window.removeEventListener('resize', resize);
};

const initParticles = () => {
  particles = [];
  for (let i = 0; i < PARTICLE_COUNT; i++) {
    const x = Math.random() * canvasA.width;
    const y = center[1] + (Math.random() * 2 - 1) * RANGE_Y;
    particles.push(new Particle(x, y));
  }
};

const renderGlow = () => {
  ctxB.save();
  ctxB.filter = 'blur(8px) brightness(200%)';
  ctxB.globalCompositeOperation = 'lighter';
  ctxB.drawImage(canvasA, 0, 0);
  ctxB.restore();

  ctxB.save();
  ctxB.filter = 'blur(4px) brightness(200%)';
  ctxB.globalCompositeOperation = 'lighter';
  ctxB.drawImage(canvasA, 0, 0);
  ctxB.restore();
};

const renderToScreen = () => {
  ctxB.save();
  ctxB.globalCompositeOperation = 'lighter';
  ctxB.drawImage(canvasA, 0, 0);
  ctxB.restore();
};

const render = () => {
  tick++;
  
  ctxA.clearRect(0, 0, canvasA.width, canvasA.height);
  
  ctxB.fillStyle = BACKGROUND_COLOR;
  ctxB.fillRect(0, 0, canvasA.width, canvasA.height);
  
  particles.forEach(particle => particle.update());
  
  renderGlow();
  renderToScreen();
  
  animationFrameId = requestAnimationFrame(render);
};

let animationFrameId = null;

onMounted(() => {
  seed(Math.random());
  const cleanup = initCanvas();
  render();
  
  onBeforeUnmount(() => {
    cleanup();
    if (animationFrameId) {
      cancelAnimationFrame(animationFrameId);
    }
  });
});
</script>

<style scoped>
.hero-container {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

.hero-text {
  position: absolute;
  top: 40px;
  left: 0;
  right: 0;
  padding: 0 40px;
  width: 100%;
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-weight: 300;
  font-size: clamp(30px, 3.5vw, 50px);
  line-height: 1.2;
  color: #fff;
  z-index: 10;
  box-sizing: border-box;
}

.footer {
  position: absolute;
  bottom: 40px;
  left: 0;
  right: 0;
  padding: 0 40px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  z-index: 10;
}

.footer-left,
.footer-right {
  display: flex;
  gap: 20px;
}

.footer-center {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
}

.footer-button {
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.3);
  color: #fff;
  padding: 8px 20px;
  border-radius: 50px;
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.footer-button:hover {
  background: rgba(255, 255, 255, 0.1);
}

.scroll-text {
  color: #fff;
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 24px;
  opacity: 0.8;
}

.content--canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  background: hsla(260,40%,5%,1);
  overflow: hidden;
}

@media screen and (max-width: 768px) {
  .hero-text {
    padding: 0 20px;
  }
  
  .footer {
    padding: 0 20px;
    flex-direction: column;
    gap: 20px;
    bottom: 20px;
  }

  .footer-center {
    position: relative;
    left: 0;
    transform: none;
  }

  .footer-right {
    justify-content: center;
  }

  .footer-left {
    justify-content: center;
  }
}
</style> 