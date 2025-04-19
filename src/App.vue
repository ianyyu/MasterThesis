<template>
  <div class="app">
    <HeroSection />
  </div>
</template>

<script setup>
import HeroSection from './components/HeroSection.vue'
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

html, body {
  overflow: hidden;
  height: 100%;
  margin: 0;
  padding: 0;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  color: var(--color-text);
  background: var(--color-bg);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.app {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  position: fixed;
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