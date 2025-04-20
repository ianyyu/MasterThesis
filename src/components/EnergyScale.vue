<template>
  <section class="energy-scale" ref="sectionRef">
    <div class="scale-container">
      <div class="scale-row">
        <div class="scale-item" ref="item0">
          <div class="value">
            <span class="number">{{ displayValues[0] }}</span>
            <span class="unit">Wh</span>
          </div>
          <div class="description">Energy used per single ChatGPT-4 query</div>
        </div>
        <div class="arrow">›</div>
        <div class="scale-item" ref="item1">
          <div class="value">
            <span class="number">{{ displayValues[1] }}</span>
            <span class="unit">Wh</span>
          </div>
          <div class="description">Equivalent to using 50 searches per day</div>
        </div>
        <div class="arrow">›</div>
        <div class="scale-item" ref="item2">
          <div class="value">
            <span class="number">{{ displayValues[2] }}</span>
            <span class="unit">Wh</span>
          </div>
          <div class="description">If every person in New York City did the same</div>
        </div>
      </div>

      <div class="comparison-text">
        To give you a better idea of what 127,500,00 Wh feels like, here are some comparisons:
      </div>

      <div class="comparisons-grid">
        <div class="comparison-item">
          Keep a 10-watt night-light glowing for ~1460 years
        </div>
        <div class="comparison-item">
          Power about 4,250 U.S. homes for one full day
        </div>
        <div class="comparison-item">
          Fully charge ~10 million iPhone 15 Pros
        </div>
        <div class="comparison-item">
          42,500 washer plus dryer loads of laundry
        </div>
        <div class="comparison-item">
          Power one household refrigerator for about 250 years
        </div>
        <div class="comparison-item">
          Run a PlayStation 5 at full blast for ~73 years
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const sectionRef = ref(null);
const item0 = ref(null);
const item1 = ref(null);
const item2 = ref(null);

const targetValues = [0.3, 15, 127500000];
const displayValues = ref(['0.0', '0', '0']);
const animationFrames = 15;
const hasAnimated = ref(false);

function formatNumber(number, index) {
  if (index === 2) {
    return Math.round(number).toLocaleString();
  }
  return number.toFixed(1);
}

function easeOutQuad(t) {
  return t * (2 - t);
}

function animateValue(index, start, end) {
  let currentFrame = 0;
  const increment = (end - start) / animationFrames;

  function update() {
    currentFrame++;
    const progress = easeOutQuad(currentFrame / animationFrames);
    const current = start + ((end - start) * progress);
    displayValues.value[index] = formatNumber(current, index);

    if (currentFrame < animationFrames) {
      requestAnimationFrame(update);
    } else {
      displayValues.value[index] = formatNumber(end, index);
    }
  }

  requestAnimationFrame(update);
}

function resetValues() {
  displayValues.value = ['0.0', '0', '0'];
  [item0.value, item1.value, item2.value].forEach(item => {
    if (item) {
      const numberEl = item.querySelector('.number');
      if (numberEl) {
        numberEl.classList.remove('visible');
      }
    }
  });
  hasAnimated.value = false;
}

function startAllAnimations() {
  if (!hasAnimated.value) {
    hasAnimated.value = true;
    [0, 1, 2].forEach((index) => {
      setTimeout(() => {
        const element = [item0.value, item1.value, item2.value][index];
        if (element) {
          element.querySelector('.number').classList.add('visible');
          animateValue(index, 0, targetValues[index]);
        }
      }, index * 150);
    });
  }
}

onMounted(() => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      // When the section exits viewport from the top
      if (!entry.isIntersecting && entry.boundingClientRect.top > 0) {
        resetValues();
      }
      // When the section enters viewport from the top
      else if (entry.isIntersecting && entry.boundingClientRect.top < window.innerHeight) {
        startAllAnimations();
      }
    });
  }, {
    threshold: 0,
    rootMargin: '-20% 0px -20% 0px' // Trigger when section is 20% in viewport
  });

  if (sectionRef.value) {
    observer.observe(sectionRef.value);
  }
});
</script>

<style scoped>
.energy-scale {
  position: relative;
  width: 100%;
  min-height: 100vh;
  background: hsla(260,40%,5%,1);
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding: 40px 0 80px 0;
}

.scale-container {
  width: calc(100% - 180px);
  max-width: 1200px;
  margin: 0 auto;
}

.scale-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 80px;
  margin-top: 0;
}

.scale-item {
  text-align: center;
  flex: 1;
}

.value {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 100px;
  font-weight: 300;
  color: #fff;
  margin-bottom: 16px;
  display: flex;
  align-items: baseline;
  justify-content: center;
}

.number {
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.6s ease, transform 0.6s ease;
}

.number.visible {
  opacity: 1;
  transform: translateY(0);
}

.unit {
  font-size: 24px;
  margin-left: 4px;
  opacity: 0.8;
}

.description {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 14px;
  color: #fff;
  opacity: 0.6;
  max-width: 200px;
  margin: 0 auto;
}

.arrow {
  font-size: 48px;
  color: #fff;
  opacity: 0.3;
  margin: 0 40px;
  font-weight: 300;
}

.comparison-text {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 16px;
  font-style: italic;
  color: #fff;
  opacity: 0.8;
  text-align: left;
  margin-bottom: 40px;
  margin-top: 40px;
  max-width: 500px;
  line-height: 1.4;
  padding: 0 20px;
}

.comparisons-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  column-gap: 40px;
  row-gap: 100px;
  max-width: 2000px;
  margin: auto;
  padding: 0 20px;
}

.comparison-item {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-weight: 300;
  font-size: 26px;
  line-height: 1.4;
  color: #ffffffdb;
  padding: 20px 0 0 0;
  border-top: 1.5px solid rgb(255, 255, 255);
  transition: border-color 0.3s ease;
}

@media screen and (max-width: 1200px) {
  .scale-container {
    width: calc(100% - 80px);
  }

  .value {
    font-size: 40px;
  }

  .unit {
    font-size: 20px;
  }

  .arrow {
    margin: 0 20px;
  }
}

@media screen and (max-width: 768px) {
  .scale-container {
    width: calc(100% - 40px);
  }

  .comparisons-grid {
    padding: 0 10px;
    grid-template-columns: 1fr;
    row-gap: 60px;
  }

  .scale-row {
    flex-direction: column;
    gap: 40px;
  }

  .arrow {
    transform: rotate(90deg);
    margin: 0;
  }

  .comparison-item {
    padding: 15px 0 0 0;
  }

  .value {
    font-size: 36px;
  }

  .unit {
    font-size: 18px;
  }

  .description {
    max-width: 280px;
  }

  .comparison-text {
    max-width: 300px;
    margin-bottom: 30px;
    padding: 0 10px;
  }
}
</style> 