<template>
  <section class="introduction" id="introduction">
    <div class="content">
      <div class="chart-container">
        <h3 class="chart-title">Energy per Query (Wh): AI vs Search vs LED</h3>
        <EnergyChart />
      </div>
      <h2 class="title">Watt's in Your Query?</h2>
      <div class="text-columns">
        <div class="column">
          <p>
Ever wonder what it "costs" when you ask an AI a question? Picture a lamp on your bedside table. A super‑efficient AI (like Google's Gemini Flash) uses about the same electricity as flashing that 10‑watt lamp on and off once. A typical chatbot is more like leaving the lamp on for two minutes, and giant models can stretch to ten.           </p>
          <p>
            One flick isn't much, but send 100 questions in a day and you've burned around 300 watt‑hours, enough juice to toast six slices of bread or fill an iPhone from empty. Multiply that by millions of curious people and you can imagine a vast hall of tiny lamps glowing all day, each one lighting up just long enough to answer someone's "Hey, AI…?"          </p>
        </div>
        <div class="column">
          <p>
            The chart translates the numbers into something tangible: each bar shows the energy consumed by one AI query, a Google search, or a minute of LED‑bulb use, so you can see at a glance how much electricity a single query requires. </p>
          <p>
            With that visual in mind, the energy behind our daily chats becomes clear and, we hope, a bit more memorable.          </p>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { defineComponent } from 'vue';
import EnergyChart from './EnergyChart.vue';
</script>

<style scoped>
.introduction {
  position: relative;
  width: 100%;
  min-height: 100vh;
  background: hsla(260,40%,5%,1);
  display: flex;
  align-items: flex-start;
  padding: 240px 0 0 0;
  box-sizing: border-box;
}

.content {
  padding-left: 120px;
  width: 100%;
}

.chart-container {
  width: calc(100% - 180px);
  height: 650px;
  margin: 60px auto 80px;
  padding: 20px 60px 40px 20px;
  position: relative;
  display: flex;
  flex-direction: column;
}

.chart-title {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-weight: 300;
  font-style: italic;
  font-size: 16px;
  color: #fff;
  opacity: 0.9;
  text-align: center;
  margin: 0 0 30px 0;
}

.title {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 50px;
  font-weight: 300;
  color: #fff;
  margin: 40px 0;
  opacity: 1;
}

.text-columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  margin-bottom: 10%;
  justify-content: space-between;
  width: 600px;
}

.column {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: #ffffffe2;
  opacity: 1;
  width: 100%;
}

.column p {
  margin-bottom: 24px;
}

.column p:last-child {
  margin-bottom: 0;
}

@media screen and (max-width: 1200px) {
  .chart-container {
    width: calc(100% - 160px);
    height: 450px;
    margin: 40px auto 120px;
    padding: 20px 40px 30px 20px;
  }

  .chart-title {
    margin: 0 0 25px 0;
  }
}

@media screen and (max-width: 768px) {
  .introduction {
    padding: 60px 0 0 0;
  }

  .content {
    padding-left: 20px;
  }
  
  .text-columns {
    grid-template-columns: 1fr;
    gap: 10px;
    justify-content: flex-start;
    width: 620px;
  }

  .column {
    max-width: 300px;
    margin: 0;
  }
  
  .title {
    font-size: 32px;
    margin: 40px 0;
  }

  .chart-container {
    width: calc(100% - 40px);
    height: 400px;
    margin: 20px auto 80px;
    padding: 15px 20px 20px 10px;
  }

  .chart-title {
    margin: 0 0 20px 0;
    font-size: 18px;
  }
}
</style> 