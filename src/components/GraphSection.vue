<template>
  <div class="graph-section">
    <div class="graph-3d">
      <div ref="plotlyDiv" id="myDiv" style="width:100%;height:80vh;"></div>
    </div>
    <div class="metric-board">
      <div class="metric-title">Energy Consumption</div>
      <div class="metric-value">{{ selectedPoint.energy }} KW</div>
      <div class="metric-section">
        <div class="section-stroke"></div>
        <div class="section-label">Prompt</div>
        <div class="section-content"><i>"{{ selectedPoint.prompt }}"</i></div>
      </div>
      <div class="metric-row">
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Model</div>
          <div class="section-content">{{ selectedPoint.model }}</div>
        </div>
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Parameter</div>
          <div class="section-content">{{ selectedPoint.parameter }}</div>
        </div>
      </div>
      <div class="metric-row">
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Example 1</div>
          <div class="section-content">{{ selectedPoint.example1 }}</div>
        </div>
        <div class="metric-section">
          <div class="section-stroke"></div>
          <div class="section-label">Example 2</div>
          <div class="section-content">{{ selectedPoint.example2 }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, reactive } from 'vue'
import Plotly from 'plotly.js-dist'

const plotlyDiv = ref(null)

// Metadata for each dot
const points = [
  {
    energy: 10,
    prompt: 'Does the series Σan converge absolutely?',
    model: 'ChatGPT 0-1 Reasoning',
    parameter: '17 Billion',
    example1: '1,000 of LED lightbulb light up simultaneously',
    example2: '7 space heaters turned on all at once.'
  },
  {
    energy: 5,
    prompt: 'What is the capital of France?',
    model: 'ChatGPT 0-1 Reasoning',
    parameter: '17 Billion',
    example1: '500 LED bulbs',
    example2: '3 space heaters.'
  },
  {
    energy: 7,
    prompt: 'Explain quantum entanglement.',
    model: 'ChatGPT 0-1 Reasoning',
    parameter: '17 Billion',
    example1: '700 LED bulbs',
    example2: '4 space heaters.'
  },
  {
    energy: 12,
    prompt: 'What is the speed of light?',
    model: 'ChatGPT 0-1 Reasoning',
    parameter: '17 Billion',
    example1: '1,200 LED bulbs',
    example2: '8 space heaters.'
  },
  {
    energy: 8,
    prompt: 'Define photosynthesis.',
    model: 'ChatGPT 0-1 Reasoning',
    parameter: '17 Billion',
    example1: '800 LED bulbs',
    example2: '5 space heaters.'
  }
]

const traces = [
  {
    x: [3], y: [4], z: [6], mode: 'markers', marker: { size: 12, line: { color: 'rgba(217, 217, 217, 0.14)', width: 0.5 }, opacity: 0.8 }, type: 'scatter3d', name: 'Dot 1',
    hovertemplate: [
      'Energy: 10 KW',
      '<br>Prompt: Does the series Σan converge absolutely?',
      '<br>Model: ChatGPT 0-1 Reasoning',
      '<br>Parameter: 17 Billion',
      '<br>Example 1: 1,000 of LED lightbulb light up simultaneously',
      '<br>Example 2: 7 space heaters turned on all at once.'
    ].join('')
  },
  {
    x: [2], y: [2], z: [2], mode: 'markers', marker: { size: 12, line: { color: 'rgba(217, 217, 217, 0.14)', width: 0.5 }, opacity: 0.8 }, type: 'scatter3d', name: 'Dot 2',
    hovertemplate: [
      'Energy: 5 KW',
      '<br>Prompt: What is the capital of France?',
      '<br>Model: ChatGPT 0-1 Reasoning',
      '<br>Parameter: 17 Billion',
      '<br>Example 1: 500 LED bulbs',
      '<br>Example 2: 3 space heaters.'
    ].join('')
  },
  {
    x: [1], y: [1], z: [1], mode: 'markers', marker: { size: 12, line: { color: 'rgba(217, 217, 217, 0.14)', width: 0.5 }, opacity: 0.8 }, type: 'scatter3d', name: 'Dot 3',
    hovertemplate: [
      'Energy: 7 KW',
      '<br>Prompt: Explain quantum entanglement.',
      '<br>Model: ChatGPT 0-1 Reasoning',
      '<br>Parameter: 17 Billion',
      '<br>Example 1: 700 LED bulbs',
      '<br>Example 2: 4 space heaters.'
    ].join('')
  },
  {
    x: [1], y: [3], z: [4], mode: 'markers', marker: { size: 12, line: { color: 'rgba(217, 217, 217, 0.14)', width: 0.5 }, opacity: 0.8 }, type: 'scatter3d', name: 'Dot 4',
    hovertemplate: [
      'Energy: 12 KW',
      '<br>Prompt: What is the speed of light?',
      '<br>Model: ChatGPT 0-1 Reasoning',
      '<br>Parameter: 17 Billion',
      '<br>Example 1: 1,200 LED bulbs',
      '<br>Example 2: 8 space heaters.'
    ].join('')
  },
  {
    x: [2], y: [5], z: [6], mode: 'markers', marker: { size: 12, line: { color: 'rgba(217, 217, 217, 0.14)', width: 0.5 }, opacity: 0.8 }, type: 'scatter3d', name: 'Dot 5',
    hovertemplate: [
      'Energy: 8 KW',
      '<br>Prompt: Define photosynthesis.',
      '<br>Model: ChatGPT 0-1 Reasoning',
      '<br>Parameter: 17 Billion',
      '<br>Example 1: 800 LED bulbs',
      '<br>Example 2: 5 space heaters.'
    ].join('')
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
      title: { text: 'Token Usage', font: { size: 8 } },
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

const selectedPoint = reactive({ ...points[0] })

onMounted(() => {
  Plotly.newPlot(plotlyDiv.value, traces, layout)
  plotlyDiv.value.on('plotly_click', (event) => {
    // event.points[0].curveNumber gives the trace index
    const traceIdx = event.points[0].curveNumber
    Object.assign(selectedPoint, points[traceIdx])
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