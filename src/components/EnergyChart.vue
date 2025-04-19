<template>
  <div class="chart-wrapper" ref="chartWrapper">
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import Chart from 'chart.js/auto';

const chartWrapper = ref(null);
const chartCanvas = ref(null);
let chart = null;
let animationFrameId = null;
let lastProgress = 0;

let lastScrollY = 0;
let scrollVelocity = 0;
let lastScrollTime = Date.now();

const createDotPattern = (color) => {
  const patternCanvas = document.createElement('canvas');
  const patternContext = patternCanvas.getContext('2d');
  const size = 6;
  const dotSize = 2.5;
  
  patternCanvas.width = size;
  patternCanvas.height = size;

  patternContext.fillStyle = 'transparent';
  patternContext.fillRect(0, 0, size, size);

  patternContext.fillStyle = color;
  patternContext.beginPath();
  patternContext.arc(size/2, size/2, dotSize/2, 0, Math.PI * 2);
  patternContext.fill();

  return patternCanvas;
};

const actualData = [2.9, 0.54, 3.24, 1.7, 0.46, 0.27, 0.81, 1.0, 0.30, 0.17];
const labels = ['ChatGPT 3.5', 'ChatGPT 4.0', 'ChatGPT o1', 'Llama 3 70B', 'Llama 4', 'Gemini 1.5 Pro', 'Claude 3.5 Sonnet', 'DeepSeek R1', 'Google Search', '1min use of LED light bulb'];

const combinedData = labels.map((label, index) => ({
  label,
  value: actualData[index]
}));

combinedData.sort((a, b) => b.value - a.value);

const sortedLabels = combinedData.map(item => item.label);
const sortedData = combinedData.map(item => item.value);

const lerp = (start, end, t) => {
  return start * (1 - t) + end * t;
};

const updateChartValues = (progress) => {
  const currentTime = Date.now();
  const timeDelta = currentTime - lastScrollTime;
  
  // Calculate adaptive smoothing based on scroll velocity
  // Faster scrolling = less smoothing = faster animation
  const velocityFactor = Math.min(1, Math.abs(scrollVelocity) / 50); // Normalize velocity
  const smoothingFactor = lerp(0.05, 0.3, velocityFactor); // Smoothing range from 0.05 (slow) to 0.3 (fast)
  
  const smoothProgress = lerp(lastProgress, progress, smoothingFactor);
  lastProgress = smoothProgress;

  chart.data.datasets[0].data = sortedData.map(value => value * smoothProgress);
  chart.update('none');

  if (Math.abs(progress - smoothProgress) > 0.001) {
    animationFrameId = requestAnimationFrame(() => updateChartValues(progress));
  }
};

const handleScroll = () => {
  const currentScroll = window.scrollY;
  const currentTime = Date.now();
  const timeDelta = currentTime - lastScrollTime;
  
  // Calculate scroll velocity (pixels per millisecond)
  scrollVelocity = (currentScroll - lastScrollY) / Math.max(1, timeDelta);
  
  lastScrollY = currentScroll;
  lastScrollTime = currentTime;
};

const animateChart = (entries) => {
  const [entry] = entries;
  
  // Reset when out of view
  if (!entry.isIntersecting || !chart) {
    lastProgress = 0;
    if (chart) {
      chart.data.datasets[0].data = Array(sortedLabels.length).fill(0);
      chart.update('none');
    }
    return;
  }

  // Cancel any ongoing animation
  if (animationFrameId) {
    cancelAnimationFrame(animationFrameId);
  }

  // Calculate progress based on how far the element is through the viewport
  const rect = entry.boundingClientRect;
  const viewportHeight = window.innerHeight;
  const progress = 1 - (rect.top / (viewportHeight - rect.height));

  // Clamp progress between 0 and 1
  const clampedProgress = Math.min(1, Math.max(0, progress));
  
  animationFrameId = requestAnimationFrame(() => updateChartValues(clampedProgress));
};

onMounted(() => {
  const ctx = chartCanvas.value.getContext('2d');
  
  // Add scroll listener
  window.addEventListener('scroll', handleScroll, { passive: true });
  
  const purplePattern = ctx.createPattern(createDotPattern('#ff00ff'), 'repeat');
  const bluePattern = ctx.createPattern(createDotPattern('#6083FF'), 'repeat');

  const chartData = {
    labels: sortedLabels,
    datasets: [{
      data: Array(sortedLabels.length).fill(0),
      backgroundColor: sortedLabels.map(label => 
        label === '1min use of LED light bulb' || label === 'Google Search' 
          ? purplePattern
          : bluePattern
      ),
      borderColor: 'transparent',
      borderWidth: 0,
      borderRadius: 4,
      barThickness: 20,
    }]
  };

  chart = new Chart(ctx, {
    type: 'bar',
    data: chartData,
    options: {
      indexAxis: 'y',
      responsive: true,
      maintainAspectRatio: false,
      animation: false,
      plugins: {
        legend: {
          display: false
        },
        tooltip: {
          callbacks: {
            label: function(context) {
              return `Energy: ${context.raw.toFixed(1)}Wh`;
            }
          }
        }
      },
      scales: {
        x: {
          grid: {
            color: 'rgba(255, 255, 255, 0.1)'
          },
          ticks: {
            color: '#fff',
            font: {
              weight: '500'
            }
          }
        },
        y: {
          grid: {
            display: false
          },
          ticks: {
            color: '#fff',
            font: {
              weight: '500'
            }
          }
        }
      }
    }
  });

  const observer = new IntersectionObserver(animateChart, {
    threshold: Array.from({ length: 60 }, (_, i) => i / 59),
    rootMargin: "-10% 0px -10% 0px"
  });

  if (chartWrapper.value) {
    observer.observe(chartWrapper.value);
  }

  onBeforeUnmount(() => {
    window.removeEventListener('scroll', handleScroll);
    if (chartWrapper.value) {
      observer.unobserve(chartWrapper.value);
    }
    if (animationFrameId) {
      cancelAnimationFrame(animationFrameId);
    }
  });
});
</script>

<style scoped>
.chart-wrapper {
  width: 100%;
  height: 100%;
  background: transparent;
  will-change: transform;
}
</style> 