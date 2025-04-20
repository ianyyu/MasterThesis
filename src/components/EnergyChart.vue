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
let startTime = null;
const ANIMATION_DURATION = 800; // Reduced from 800ms for snappier animation
const BAR_DELAY = 100; // Reduced from 100ms for smoother sequence

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

// Smooth easing function
const easeOutQuart = (x) => {
  return 1 - Math.pow(1 - x, 4);
};

const updateChartValues = () => {
  const rect = chartWrapper.value.getBoundingClientRect();
  const viewportHeight = window.innerHeight;
  
  // Start animation when the top of the chart is 80% of the way up the viewport
  // Complete animation when it's 20% of the way up
  const startPoint = viewportHeight * 0.8;
  const endPoint = viewportHeight * 0.2;
  const distance = startPoint - endPoint;
  
  // Calculate base progress
  let progress = (startPoint - rect.top) / distance;
  progress = Math.min(1, Math.max(0, progress));
  
  // Apply easing
  const easedProgress = easeOutQuart(progress);
  
  // Apply different delays to each bar
  const newData = sortedData.map((value, index) => {
    // Delay each bar by staggering its progress
    const delayedProgress = Math.max(0, (easedProgress * 1.2) - (index * 0.1));
    return value * Math.min(1, delayedProgress);
  });

  chart.data.datasets[0].data = newData;
  chart.update('none');

  animationFrameId = requestAnimationFrame(updateChartValues);
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
  
  if (!entry.isIntersecting || !chart) {
    if (chart) {
      chart.data.datasets[0].data = Array(sortedLabels.length).fill(0);
      chart.update('none');
    }
    if (animationFrameId) {
      cancelAnimationFrame(animationFrameId);
      animationFrameId = null;
    }
    return;
  }

  if (!animationFrameId) {
    animationFrameId = requestAnimationFrame(updateChartValues);
  }
};

onMounted(() => {
  const ctx = chartCanvas.value.getContext('2d');
  
  // Add scroll listener
  window.addEventListener('scroll', handleScroll, { passive: true });
  
  const purplePattern = ctx.createPattern(createDotPattern('#ff00ff'), 'repeat');
  const bluePattern = ctx.createPattern(createDotPattern('#6083FF'), 'repeat');

  const maxValue = Math.max(...sortedData);

  const chartData = {
    labels: sortedLabels,
    datasets: [{
      data: Array(sortedLabels.length).fill(0),
      actualData: sortedData, // Store the actual values for tooltips
      backgroundColor: sortedLabels.map(label => 
        label === '1min use of LED light bulb' || label === 'Google Search' 
          ? purplePattern
          : bluePattern
      ),
      tooltipColor: sortedLabels.map(label => 
        label === '1min use of LED light bulb' || label === 'Google Search' 
          ? '#ff00ff'
          : '#6083FF'
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
      layout: {
        padding: {
          top: 20,    // Add padding at the top
          right: 0,
          bottom: 0,
          left: 0
        }
      },
      plugins: {
        legend: {
          display: false
        },
        tooltip: {
          callbacks: {
            label: function(context) {
              const actualValue = context.dataset.actualData[context.dataIndex];
              return `Energy: ${actualValue.toFixed(1)}Wh`;
            },
            labelColor: function(context) {
              return {
                backgroundColor: context.dataset.tooltipColor[context.dataIndex],
                borderColor: 'transparent',
                borderWidth: 0,
                borderRadius: 2,
                borderSkipped: false,
                boxShadow: 'none'
              };
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
          },
          min: 0,
          max: maxValue * 1.1,
          beginAtZero: true
        },
        y: {
          grid: {
            display: false
          },
          ticks: {
            color: '#fff',
            font: {
              weight: '500'
            },
            padding: 10  // Add some padding to the labels
          },
          afterFit: function(scaleInstance) {
            // Ensure there's enough space for the bars
            scaleInstance.paddingTop = 15;
            scaleInstance.paddingBottom = 15;
          }
        }
      }
    }
  });

  const observer = new IntersectionObserver(animateChart, {
    threshold: [0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0],
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