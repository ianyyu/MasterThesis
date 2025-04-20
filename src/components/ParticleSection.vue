<template>
  <div class="particle-background" :class="{ 'is-visible': isVisible }">
    <canvas ref="canvas" class="particle-canvas"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const canvas = ref(null);
let ctx = null;
let animationFrameId = null;
let particles = [];
const isVisible = ref(false);
const isEnergyCalVisible = ref(false);

const NUM_PARTICLES = 80;
const PARTICLE_COLORS = ['#CD46FF', '#2A7EFF'];
const MIN_RADIUS = 0.8;
const MAX_RADIUS = 2;
const MIN_SPEED = 2.0;
const MAX_SPEED = 20.0;
const WAVE_AMPLITUDE = 70;

class Particle {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.reset();
  }

  reset() {
    this.baseX = this.x;
    this.speed = rand(MIN_SPEED, MAX_SPEED);
    this.alpha = rand(0.8, 1.0);
    this.maxAlpha = this.alpha;
    this.startY = this.y;
    this.baseRadius = rand(MIN_RADIUS, MAX_RADIUS);
    this.waveOffset = Math.random() * Math.PI * 2;
    this.color = PARTICLE_COLORS[Math.floor(Math.random() * PARTICLE_COLORS.length)];
    this.glowSize = this.baseRadius * 3; // Glow size relative to particle size
  }

  update() {
    this.y -= this.speed;
    
    const waveProgress = (this.startY - this.y) / window.innerHeight;
    this.x = this.baseX + Math.sin(waveProgress * Math.PI * 2 + this.waveOffset) * WAVE_AMPLITUDE;
    
    const progress = (this.startY - this.y) / window.innerHeight;
    const fadeProgress = Math.min(1, progress * 1.5);
    this.alpha = this.maxAlpha * (1 - Math.pow(fadeProgress, 2));
    
    const sizeProgress = Math.sin(progress * Math.PI * 2 + this.waveOffset) * 0.2;
    this.r = this.baseRadius * (1 + sizeProgress);
    this.glowSize = this.r * 2; // Update glow size with particle size
    
    if (this.y < -this.r) {
      // Reset particle at the bottom with new random x position
      this.y = window.innerHeight + this.r;
      this.x = Math.random() * window.innerWidth;
      this.reset();
    }
  }

  draw() {
    // Draw glow
    const gradient = ctx.createRadialGradient(
      this.x, this.y, 0,
      this.x, this.y, this.glowSize
    );
    gradient.addColorStop(0, `${this.color}${Math.floor(this.alpha * 255).toString(16).padStart(2, '0')}`);
    gradient.addColorStop(1, `${this.color}00`); // Fully transparent at edge
    
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.glowSize, 0, Math.PI * 2);
    ctx.fillStyle = gradient;
    ctx.fill();
    
    // Draw particle
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.r, 0, Math.PI * 2);
    ctx.fillStyle = `${this.color}${Math.floor(this.alpha * 255).toString(16).padStart(2, '0')}`;
    ctx.fill();
  }
}

const rand = (min, max) => Math.random() * (max - min) + min;

function init() {
  if (!ctx) return;
  
  canvas.value.width = window.innerWidth;
  canvas.value.height = window.innerHeight;
  
  particles = [];
  
  // Distribute particles evenly across the screen
  for (let i = 0; i < NUM_PARTICLES; i++) {
    const x = Math.random() * window.innerWidth;
    const y = Math.random() * window.innerHeight;
    particles.push(new Particle(x, y));
  }
}

function animate() {
  if (!ctx) return;
  
  ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
  ctx.globalCompositeOperation = 'screen';
  
  for (const p of particles) {
    p.update();
    p.draw();
  }
  
  animationFrameId = requestAnimationFrame(animate);
}

function handleResize() {
  if (canvas.value) {
    canvas.value.width = window.innerWidth;
    canvas.value.height = window.innerHeight;
    init();
  }
}

function checkVisibility() {
  const heroSection = document.querySelector('.hero-section');
  if (heroSection) {
    const heroRect = heroSection.getBoundingClientRect();
    // Only show particles if we're past hero AND not in EnergyCal section
    isVisible.value = heroRect.bottom <= 0 && !isEnergyCalVisible.value;
  }
}

// Method to be called from parent when EnergyCal visibility changes
const updateVisibility = (shouldShow) => {
  isEnergyCalVisible.value = !shouldShow;
  checkVisibility(); // Recheck visibility with new EnergyCal state
};

defineExpose({
  updateVisibility
});

onMounted(() => {
  if (canvas.value) {
    ctx = canvas.value.getContext('2d');
    init();
    animate();
    window.addEventListener('resize', handleResize);
    window.addEventListener('scroll', checkVisibility);
    // Initial check
    checkVisibility();
  }
});

onBeforeUnmount(() => {
  if (animationFrameId) {
    cancelAnimationFrame(animationFrameId);
  }
  window.removeEventListener('resize', handleResize);
  window.removeEventListener('scroll', checkVisibility);
});
</script>

<style scoped>
.particle-background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: transparent;
  overflow: hidden;
  z-index: 2;
  pointer-events: none;
  mix-blend-mode: screen;
  opacity: 0;
  transition: opacity 0.5s ease-in-out;
  filter: blur(1px); /* Add slight blur to entire canvas */
}

.particle-background.is-visible {
  opacity: 1;
}

.particle-canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: block;
}
</style> 