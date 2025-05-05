<template>
  <section class="energy-cal" ref="sectionRef">
    <div class="content">
      <h2 class="title">
        A Deeper Look at<br>
        Energy Use
      </h2>
      <div class="main-content">
        <div class="animation-container">
          <div class="wrap">
            <!-- Generate 300 particle elements -->
            <div v-for="n in 300" :key="n" class="c" :class="`p${n}`"></div>
          </div>
        </div>
        <div class="text-columns">
          <div class="column">
            <p>
              In practice, the energy cost of an AI query is far more nuanced than a single headline number. It depends on at least two key variables: (1) model complexity: how many parameters and embedded vectors the model contains, and (2) the volume of text it produces (output tokens).
            </p>
            <p>
              To make that relationship concrete, the next section introduces a simple illustrative formula that combines these factors into one "energy" metric.
            </p>
          </div>
          <div class="column">
            <p>
              The equation is intentionally linear and purely hypothetical; it isn't calibrated to real-world measurements, but it does highlight how rising complexity or greater token usage can drive up consumption.
            </p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const sectionRef = ref(null);
const emit = defineEmits(['updateParticleVisibility']);

function generateParticleStyles() {
  const total = 480;
  const orbSize = 140;
  let styles = '';

  for (let i = 1; i <= total; i++) {
    const z = Math.random() * 360;
    const y = Math.random() * 360;
    const color = i % 2 === 0 ? '#2A7EFF' : '#CD46FF';
    
    styles += `
      .p${i} {
        animation: orbit${i} 20s infinite;
        animation-delay: ${i * 0.01}s;
        background-color: ${color};
      }
    `;

    styles += `
      @keyframes orbit${i} {
        0% {
          opacity: 0;
          transform: rotateZ(-${z}deg) rotateY(${y}deg) translateX(${orbSize * 3}px) rotateZ(${z}deg);
        }
        20% {
          opacity: 1;
          transform: rotateZ(-${z}deg) rotateY(${y}deg) translateX(${orbSize}px) rotateZ(${z}deg);
        }
        80% {
          opacity: 1;
          transform: rotateZ(-${z + 180}deg) rotateY(${y + 180}deg) translateX(${orbSize}px) rotateZ(${z + 180}deg);
        }
        100% {
          opacity: 0;
          transform: rotateZ(-${z + 360}deg) rotateY(${y + 360}deg) translateX(${orbSize * 3}px) rotateZ(${z + 360}deg);
        }
      }
    `;
  }

  const styleEl = document.createElement('style');
  styleEl.textContent = styles;
  document.head.appendChild(styleEl);

  return () => {
    document.head.removeChild(styleEl);
  };
}

onMounted(() => {
  const cleanup = generateParticleStyles();

  const observer = new IntersectionObserver((entries) => {
    const [entry] = entries;
    emit('updateParticleVisibility', !entry.isIntersecting);
  }, {
    threshold: 0.1
  });

  if (sectionRef.value) {
    observer.observe(sectionRef.value);
  }

  onBeforeUnmount(() => {
    cleanup();
    if (sectionRef.value) {
      observer.unobserve(sectionRef.value);
    }
  });
});
</script>

<style scoped>
.energy-cal {
  position: relative;
  width: 100%;
  background: hsla(260,40%,5%,1);
  display: flex;
  align-items: flex-start;
  padding: 40px 0 0 0;
  box-sizing: border-box;
  margin-bottom: 40px;
}

.content {
  width: 100%;
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 40px;
}

.title {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 50px;
  font-weight: 300;
  color: #fff;
  margin: 0 0 120px 0;
  line-height: 1.2;
  width: 400px;
}

.main-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 80px;
  align-items: start;
  padding-bottom: 0;
}

.animation-container {
  width: 100%;
  height: 400px;
  position: relative;
  overflow: hidden;
  background: transparent;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-left: -40px;
  margin-top: -40px;
}

.wrap {
  position: absolute;
  width: 0;
  height: 0;
  transform-style: preserve-3d;
  perspective: 1000px;
  animation: rotate 30s infinite linear;
  transform: scale(1.5);
}

.c {
  position: absolute;
  width: 3px;
  height: 3px;
  border-radius: 50%;
  opacity: 0;
}

@keyframes rotate {
  100% {
    transform: scale(1.5) rotateY(360deg);
  }
}

.text-columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  width: 580px;
}

.column {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: #fff;
  width: 100%;
}

.column p {
  margin-bottom: 24px;
}

.column p:last-child {
  margin-bottom: 0;
}

@media screen and (max-width: 1200px) {
  .content {
    padding: 0 20px;
  }

  .title {
    font-size: 48px;
    margin-bottom: 60px;
    width: 300px;
  }

  .main-content {
    gap: 40px;
  }

  .animation-container {
    margin-left: -40px;
    margin-top: -40px;
  }
}

@media screen and (max-width: 768px) {
  .energy-cal {
    padding: 40px 0 0 0;
  }

  .title {
    font-size: 32px;
    margin-bottom: 40px;
    width: 100%;
  }

  .main-content {
    grid-template-columns: 1fr;
    gap: 40px;
  }

  .animation-container {
    margin-left: 0;
    margin-top: 0;
    height: 300px;
  }

  .text-columns {
    grid-template-columns: 1fr;
    gap: 20px;
  }
}
</style>

<!-- Dynamic styles for particle animations -->
<style>
/* This style block will be generated on component mount */
</style> 