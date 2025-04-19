<template>
  <section class="introduction">
    <div class="content">
      <h2 class="title">Watt's in Your Query?</h2>
      <div class="text-columns">
        <div class="column">
          <p>
            Ever wonder what it "costs" when you ask an AI a question? Picture a lamp on your bedside table. A super-efficient AI like Google's Gemini Flash uses about the same electricity as leaving that in. What's wild is that one chat with these models can stretch to ten.
          </p>
          <p>
            One tick of a watch, but send 100 questions in a day and you've burned about 300 watt-hours, enough juice to toast six slices of bread or fill an iPhone from empty. Start multiplying that by millions of curious people and you can imagine a vast hall of tiny lamps glowing all day, each one lighting up just long enough to answer someone's "Hey AI..."
          </p>
        </div>
        <div class="column">
          <p>
            The chart above turns those numbers into something you can feel: every bar pairs an AI model with an everyday object light bulb flashes, Google searches, toaster seconds, so you can instantly see how much energy each interaction takes.
          </p>
          <p>
            With that visual in mind, the energy behind our daily chats becomes clear and, we hope, a bit more memorable.
          </p>
        </div>
      </div>
      <div class="chart-container">
        <EnergyChart />
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
  padding: 80px 0 0 0;
  box-sizing: border-box;
}

.content {
  padding-left: 40px;
  width: 100%;
}

.title {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 50px;
  font-weight: 300;
  color: #fff;
  margin-bottom: 40px;
  opacity: 1;
}

.text-columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 32px;
  margin-bottom:10%;
  justify-content: space-between;
  width: 700px;
}

.column {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: #fff;
  opacity: 1;
  /*max-width: 300px;*/
  width: 100%;
}

.column p {
  margin-bottom: 24px;
}

.column p:last-child {
  margin-bottom: 0;
}

.chart-container {
  width: calc(100% - 280px);
  height: 400px;
  margin: 60px auto;
  padding-right: 40px;
}

@media screen and (max-width: 1200px) {
  .chart-container {
    width: calc(100% - 160px);
  }
}

@media screen and (max-width: 768px) {
  .introduction {
    padding: 40px 0 0 0;
  }

  .content {
    padding-left: 20px;
  }
  
  .text-columns {
    grid-template-columns: 1fr;
    gap: 10px;
    justify-content: flex-start;
  }

  .column {
    max-width: 300px;
    margin: 0;
  }
  
  .title {
    font-size: 32px;
    margin-bottom: 30px;
  }

  .chart-container {
    width: calc(100% - 40px);
    margin: 40px auto;
    padding-right: 20px;
  }
}
</style> 