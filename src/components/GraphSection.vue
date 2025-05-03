<template>
  <div class="graph-section">
    <div class="graph-3d">
      <div ref="plotlyDiv" id="myDiv" style="width:100%;height:80vh;"></div>
    </div>
    <div class="metric-board">
      <div class="metric-title">Energy Consumption</div>
      <div class="metric-value">{{ points[selectedIdx].energy }} KW</div>
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
          <div class="section-label">Example 2</div>
          <div class="section-content">{{ points[selectedIdx].example2 }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, nextTick } from 'vue'
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

// Prepare data for Plotly
const x = points.map(p => p.tokenOutput)
const y = points.map(p => {
  // Use parameter as a proxy for model complexity (convert to number for plotting)
  // Remove non-numeric characters and convert to number
  const num = parseFloat((p.parameter || '').replace(/[^\d.]/g, ''))
  // Trillion = 1e12, Billion = 1e9
  if (p.parameter.includes('Trillion')) return num * 1e12
  if (p.parameter.includes('Billion')) return num * 1e9
  return num
})
const z = points.map(p => p.energy)

const traces = [
  {
    x,
    y,
    z,
    mode: 'markers',
    marker: { size: 12, line: { color: 'rgba(217, 217, 217, 0.14)', width: 0.5 }, opacity: 0.8 },
    type: 'scatter3d',
    customdata: points.map(p => [
      p.energy,
      p.prompt,
      p.model,
      p.parameter,
      p.tokenOutput,
      p.example2
    ]),
    hovertemplate: '<b>Model:</b> %{customdata[2]}<br>' +
                   '<b>Prompt:</b> %{customdata[1]|(.{60})...}<br>' + // Truncate prompt
                   '<b>Energy:</b> %{customdata[0]} KW<br>' +
                   '<b>Tokens:</b> %{customdata[4]}<br>' +
                   '<b>Params:</b> %{customdata[3]}<br>' +
                   '<extra></extra>', // Hide default hover info
  }
]

const layout = {
  margin: { l: 0, r: 0, b: 0, t: 0 },
  paper_bgcolor: 'rgba(0,0,0,0)',
  plot_bgcolor: 'rgba(0,0,0,0)',
  font: { color: '#fff', family: 'Inter, sans-serif' },
  showlegend: false,
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

onMounted(() => {
  Plotly.newPlot(plotlyDiv.value, traces, layout)

  // Remove any previous listeners to avoid duplicates
  plotlyDiv.value.removeAllListeners && plotlyDiv.value.removeAllListeners('plotly_click')

  plotlyDiv.value.on('plotly_click', (event) => {
    if (event.points && event.points[0]) {
      // Log the entire point data object to inspect its structure
      // console.log('Clicked point data:', event.points[0]); // Remove inspection log
      const idx = event.points[0].pointNumber; // Use pointNumber instead of pointIndex
      console.log('Clicked index:', idx, points[idx]); // Restore original log
      if (typeof idx === 'number' && points[idx]) {
        // Try updating within nextTick
        nextTick(() => {
           selectedIdx.value = idx
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
</style> 