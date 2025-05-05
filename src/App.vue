<template>
  <div class="app">
    <HeroSection class="hero-section" />
    <ParticleBackground ref="particleBackground" />
    <div class="main-content">
      <Introduction />
      <EnergyScale />
      <EnergyCal @update-particle-visibility="updateParticleVisibility" />
      <EquationSection />
      <DataExplorationText />
      <GraphSection />
      <EndingSection />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import HeroSection from './components/HeroSection.vue'
import Introduction from './components/Introduction.vue'
import EnergyScale from './components/EnergyScale.vue'
import ParticleBackground from './components/ParticleSection.vue'
import EnergyCal from './components/EnergyCal.vue'
import EquationSection from './components/EquationSection.vue'
import DataExplorationText from './components/DataExplorationText.vue'
import GraphSection from './components/GraphSection.vue'
import EndingSection from './components/EndingSection.vue'

const particleBackground = ref(null);

const updateParticleVisibility = (isVisible) => {
  if (particleBackground.value) {
    particleBackground.value.updateVisibility(isVisible);
  }
};

onMounted(() => {
  // Configure MathJax first
  window.MathJax = {
    tex: {
      inlineMath: [['\\(', '\\)']],
      packages: ['base', 'ams', 'noerrors', 'noundefined']
    },
    svg: {
      fontCache: 'global'
    },
    startup: {
      ready: () => {
        console.log('MathJax is loaded and ready');
        MathJax.startup.defaultReady();
      }
    }
  };

  // Then load MathJax script
  const script = document.createElement('script');
  script.src = 'https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-svg.js';
  script.async = true;
  script.id = 'MathJax-script';
  document.head.appendChild(script);
});
</script>

<style>
:root {
  --color-text: #fff;
  --color-bg: hsla(260,40%,5%,1);
  --color-link: #f9f9f9;
  --color-link-hover: #fff;
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  color: var(--color-text);
  background: var(--color-bg);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.app {
  width: 100%;
  min-height: 100vh;
  position: relative;
}

.main-content {
  position: relative;
  width: 100%;
  min-height: 100vh;
  background: var(--color-bg);
  z-index: 1;
}

.main-content > * {
  position: relative;
  z-index: 1;
}

main {
  position: relative;
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.frame {
  position: relative;
  z-index: 1;
}

@media screen and (min-width: 53em) {
  .frame {
    position: fixed;
    text-align: left;
    z-index: 100;
    top: 0;
    left: 0;
    display: grid;
    align-content: space-between;
    width: 100%;
    max-width: none;
    height: 100vh;
    padding: 3rem 4rem;
    pointer-events: none;
    grid-template-columns: 50% 50%;
    grid-template-rows: auto auto auto;
    grid-template-areas: '... ...'
                        '... ...'
                        '... ...';
  }
}
</style> 