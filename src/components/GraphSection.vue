<template>
  <div class="graph-section">
    <div class="graph-3d">
      <div ref="plotlyDiv" id="myDiv" style="width:100%;height:80vh;"></div>
    </div>
    <div class="metric-board">
      <div class="metric-title">Energy Consumption</div>
      <div class="metric-value">{{ displayedEnergy.toFixed(3) }} KW</div>
      <div class="metric-section">
        <div class="section-stroke"></div>
        <div class="section-label">Prompt</div>
        <div class="section-content"><i>"{{ points[selectedIdx].prompt }}"</i></div>
      </div>
      <div class="metric-row">
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Model</div>
          <div class="section-content">{{ points[selectedIdx].model }}</div>
        </div>
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Parameter</div>
          <div class="section-content">{{ points[selectedIdx].parameter }}</div>
        </div>
      </div>
      <div class="metric-row">
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Token Output</div>
          <div class="section-content">{{ points[selectedIdx].tokenOutput }}</div>
        </div>
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Example</div>
          <div class="section-content">{{ points[selectedIdx].example2 }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, nextTick, watch } from 'vue'
import Plotly from 'plotly.js-dist'

const plotlyDiv = ref(null)

// Data from Data.csv
const points = [
  {
    prompt: 'Design a 10-year global macro-economic simulation that integrates climate-risk scenarios, demographic shifts, and AI-driven productivity, then output policy recommendations for both developed and emerging economies.',
    model: 'ChatGPT o1',
    parameter: '200 Billion',
    tokenOutput: 3479,
    energy: 4.127,
    example2: 'Charge a phone up to 40% battery'
  },
  {
    prompt: 'Draft a full-stack architectural blueprint for a decentralized healthcare data exchange that satisfies HIPAA/GDPR, uses homomorphic encryption for queries, and includes a venture-scale business plan.',
    model: 'ChatGPT o1',
    parameter: '200 Billion',
    tokenOutput: 3580,
    energy: 3.732,
    example2: 'Streaming a 20-min sitcom'
  },
  {
    prompt: 'Generate a graduate-level curriculum (syllabi, readings, labs) for teaching quantum-error-corrected algorithms to computer-science majors coming from classical backgrounds.',
    model: 'ChatGPT 4o',
    parameter: '1.76 Trillion',
    tokenOutput: 1700,
    energy: 3.025,
    example2: 'Making a large latte with an espresso machine'
  },
  {
    prompt: 'Given gene-expression data (CSV), identify novel biomarkers for early Alzheimers using unsupervised learning and propose a follow-up wet-lab protocol.',
    model: 'ChatGPT 4o',
    parameter: '1.76 Trillion',
    tokenOutput: 2160,
    energy: 2.737,
    example2: 'Light LED lightblub for 16 mins'
  },
  {
    prompt: 'Write a 5-chapter interactive fiction game in Twine where player choices are influenced by a dynamic moral-alignment engine; include JSON state diagrams.',
    model: 'Claud 3.5 Sonnet',
    parameter: '175 Billion',
    tokenOutput: 2014,
    energy: 2.332,
    example2: 'Boiling half a liter of water in a 2 kW kettle'
  },
  {
    prompt: 'Plan a two-week cultural-immersion itinerary through lesser-known regions of Japan, optimizing for public transport, seasonal festivals, and vegan dining options.',
    model: 'Llama 3.2',
    parameter: '90 Billion',
    tokenOutput: 1720,
    energy: 1.913,
    example2: 'Running a Wi-Fi router for an hour'
  },
  {
    prompt: 'Explain why Gödels incompleteness theorems matter to modern software verification in terms a senior product manager can pitch to executives.',
    model: 'ChatGPT 4o',
    parameter: '1.76 Trillion',
    tokenOutput: 1520,
    energy: 1.515,
    example2: 'Charging wireless earbuds case twice'
  },
  {
    prompt: 'Create a personalized 12-week marathon training schedule for a 42-year-old with mild asthma and a history of IT-band issues.',
    model: 'Llama 3.2',
    parameter: '90 Billion',
    tokenOutput: 2020,
    energy: 1.192,
    example2: 'One cycle of an electric toothbrush'
  },
  {
    prompt: 'Compose a sonnet that hides the digits of π in an acrostic down the left margin.',
    model: 'ChatGPT 4o',
    parameter: '1.76 Trillion',
    tokenOutput: 1120,
    energy: 0.752,
    example2: 'A smart-light hub overnight standby'
  },
  {
    prompt: 'Recommend one great mystery novel for a rainy weekend.',
    model: 'Llama 3.2',
    parameter: '90 Billion',
    tokenOutput: 800,
    energy: 0.384,
    example2: 'Light a LED light blubl for 2.5 mins'
  }
]

// --- Group data by model and assign colors ---
const models = [...new Set(points.map(p => p.model))]; // Get unique model names
const colors = [
  '#2A7EFF', '#CD46FF', '#9346FF', '#5BC0FF', 
  
]; // Define a color palette

const modelColorMap = {};
models.forEach((model, index) => {
  modelColorMap[model] = colors[index % colors.length]; // Assign colors cyclically
});
console.log('Model Color Map:', modelColorMap); // Log the generated map

const traces = models.map(model => {
  const modelPoints = points.filter(p => p.model === model);
  const traceColor = modelColorMap[model]; // Get color for this trace
  console.log(`Trace for ${model}, Color: ${traceColor}`); // Log trace color
  return {
    x: modelPoints.map(p => p.tokenOutput),
    y: modelPoints.map(p => {
      const num = parseFloat((p.parameter || '').replace(/[^\d.]/g, ''))
      if (p.parameter.includes('Trillion')) return num * 1e12
      if (p.parameter.includes('Billion')) return num * 1e9
      return num
    }),
    z: modelPoints.map(p => p.energy),
    mode: 'markers',
    marker: {
      size: 10,
      color: traceColor, // Assign model-specific color
      line: { color: '#FFFFFF', width: 0.5 }, // Change stroke color to white
      opacity: 0.8
    },
    type: 'scatter3d',
    name: model, // Set trace name for legend
    customdata: modelPoints.map(p => [
      p.energy,
      p.prompt,
      p.model,
      p.parameter,
      p.tokenOutput,
      p.example2
    ]),
    hovertemplate: '<b>Model:</b> %{customdata[2]}<br>' +
                   '<b>Energy:</b> %{customdata[0]:.3f} KW<br>' +
                   '<b>Tokens:</b> %{customdata[4]}<br>' +
                   '<b>Params:</b> %{customdata[3]}<br>' +
                   '<extra></extra>', // Hide default hover info
  };
});
// --- End grouping and trace creation ---

const layout = {
  margin: { l: 0, r: 0, b: 0, t: 0 },
  paper_bgcolor: 'rgba(0,0,0,0)',
  plot_bgcolor: 'rgba(0,0,0,0)',
  font: { color: '#fff', family: 'Inter, sans-serif' },
  showlegend: true, // Enable the legend
  legend: { 
    bgcolor: 'rgba(0,0,0,0.5)', // Semi-transparent background for legend
    bordercolor: 'rgba(255,255,255,0.2)',
    borderwidth: 1,
    x: 0.05, // Position legend bottom-left
    y: 0.05,
    font: { size: 10 }
  },
  scene: {
    xaxis: {
      title: { text: 'Token Output', font: { size: 8 } },
      color: '#fff',
      gridcolor: '#333',
      zerolinecolor: '#444',
      linecolor: '#888',
      tickcolor: '#888',
      backgroundcolor: 'rgba(0,0,0,0)',
      tickfont: { size: 6 },
    },
    yaxis: {
      title: { text: 'Model Complexity', font: { size: 8 } },
      color: '#fff',
      gridcolor: '#333',
      zerolinecolor: '#444',
      linecolor: '#888',
      tickcolor: '#888',
      backgroundcolor: 'rgba(0,0,0,0)',
      tickfont: { size: 6 },
    },
    zaxis: {
      title: { text: 'Energy Consumption', font: { size: 8 } },
      color: '#fff',
      gridcolor: '#333',
      zerolinecolor: '#444',
      linecolor: '#888',
      tickcolor: '#888',
      backgroundcolor: 'rgba(0,0,0,0)',
      tickfont: { size: 6 },
    },
    bgcolor: 'rgba(0,0,0,0)',
  }
}

const selectedIdx = ref(0)
const displayedEnergy = ref(points[0].energy)

// --- Style Constants for Selection ---
const originalOpacity = 0.8;
const fadedOpacity = 0.2;
const originalLineWidth = 0.5;
const selectedLineWidth = 2;
// --- End Style Constants ---

// --- Animation Functions ---
const animationDuration = 500; // Faster duration: 0.5 seconds

function easeOutQuad(t) {
  return t * (2 - t);
}

function animateValue(start, end) {
  let startTime = null;
  const range = end - start;

  function update(currentTime) {
    if (startTime === null) startTime = currentTime;
    const elapsedTime = currentTime - startTime;
    
    // Calculate progress, ensuring it doesn't exceed 1
    const rawProgress = Math.min(1, elapsedTime / animationDuration);
    const easedProgress = easeOutQuad(rawProgress);
    
    displayedEnergy.value = start + (range * easedProgress);

    if (elapsedTime < animationDuration) {
      requestAnimationFrame(update);
    } else {
      displayedEnergy.value = end; // Ensure final value is exact
    }
  }
  requestAnimationFrame(update);
}
// --- End Animation Functions ---

// --- Function to Update Point Styles ---
// --- End Function to Update Point Styles ---

// Watch for changes in selectedIdx to trigger animation and style changes
watch(selectedIdx, (newIdx, oldIdx) => {
  // Animate Energy Value
  if (newIdx !== oldIdx && points[newIdx]) {
    const startValue = 0; 
    const endValue = points[newIdx].energy;
    animateValue(startValue, endValue);
  }

  // Update Point Styles by calling the dedicated function
  updatePointStyles(newIdx);
});

onMounted(() => {
  Plotly.newPlot(plotlyDiv.value, traces, layout)
    .then(() => {
      // Ensure plot is rendered before setting initial styles
      console.log('Plot created, setting initial styles.');
      updatePointStyles(selectedIdx.value); // Set initial highlight for point 0
    });
  
  // Click handler for multiple traces (find original index via customdata)
  plotlyDiv.value.removeAllListeners && plotlyDiv.value.removeAllListeners('plotly_click')

  plotlyDiv.value.on('plotly_click', (event) => {
    if (event.points && event.points[0]) {
      // Get the custom data of the clicked point
      const clickedCustomData = event.points[0].customdata;
      // Find the index in the original 'points' array by matching unique data (e.g., prompt and energy)
      const originalIndex = points.findIndex(p => 
        p.prompt === clickedCustomData[1] && p.energy === clickedCustomData[0]
      );

      console.log('Clicked original index:', originalIndex, points[originalIndex]);
      if (typeof originalIndex === 'number' && originalIndex !== -1 && points[originalIndex]) {
        nextTick(() => {
          selectedIdx.value = originalIndex
        });
      }
    }
  })
})
</script>

<style scoped>
.graph-section {
  display: flex;
  width: 100vw;
  height: 100vh;
  color: #fff;
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  padding-left: 5vw;
  padding-right: 5vw;
  box-sizing: border-box;
}
.graph-3d {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
}
#myDiv {
  width: 100%;
  height: 80vh;
  border-radius: 12px;
}
.button {
  display: none;
}
.metric-board {
  flex: 1;
  padding: 3rem 2rem;
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
  display: flex;
  flex-direction: column;
  justify-content: center;
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
}
.metric-title {
  font-size: 16px;
  color: #fff;
  font-weight: 300;
  font-style: italic;
  opacity: 0.9;
  margin-bottom: 0.5rem;
}
.metric-value {
  font-size: 80px;
  font-weight: 300;
  color: #fff;
  margin-bottom: 2rem;
  opacity: 1;
}
.metric-section {
  margin-top: 1.2rem;
}
.metric-section .section-label {
  font-size: 16px;
  color: #aaa;
  font-weight: 300;
  margin-bottom: 0.2rem;
  margin-top: 0;
}
.metric-section .section-content {
  font-size: 18px;
  color: #ffffffe2;
  line-height: 1.6;
  font-weight: 300;
  margin-bottom: 0.7rem;
}
.metric-section .section-stroke {
  border: none;
  border-top: 1px solid #444;
  margin-bottom: 1.2rem;
  margin-top: 0;
  width: 100%;
}
.metric-row {
  display: flex;
  justify-content: space-between;
  margin-top: 1.2rem;
  gap: 1.2rem;
}
.metric-row .metric-section {
  flex: 1;
  margin-right: 0;
}
hr {
  display: none;
}

/* Style for the prompt content to prevent height changes */
.metric-board > .metric-section:first-of-type > .section-content {
  max-height: 150px; /* Adjust this value as needed */
  overflow-y: auto;
  /* Add some padding so scrollbar doesn't overlap text */
  padding-right: 10px; 
  /* Optional: Basic scrollbar styling for webkit */
  &::-webkit-scrollbar {
    width: 6px;
  }
  &::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 3px;
  }
  &::-webkit-scrollbar-thumb {
    background-color: rgba(255, 255, 255, 0.3);
    border-radius: 3px;
  }
  /* Optional: Basic scrollbar styling for Firefox */
  scrollbar-width: thin;
  scrollbar-color: rgba(255, 255, 255, 0.3) rgba(255, 255, 255, 0.1);
}
</style> 