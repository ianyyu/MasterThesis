<template>
  <section class="data-exploration">
    <div class="content">
      <h2 class="title">Data Exploration</h2>
      <div class="text-columns">
        <div class="column">
          <p>
            Let's apply the equation to real-world queries and visualize the results. Remember, this formula is only illustrative; it simply helps us explore how energy consumption, AI-model complexity, and output tokens interact.
          </p>
        </div>
        <div class="column">
          <p>
            The 3D visualization shows the relationship between these three variables, allowing us to see patterns and trade-offs in energy usage across different model sizes & output tokens. Feel free to click on any point to see the exact values.
          </p>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
// No special functionality needed for this component
</script>

<style scoped>
.data-exploration {
  position: relative;
  width: 100%;
  background: hsla(260,40%,5%,1);
  display: flex;
  align-items: flex-start;
  padding: 80px 0 0 0;
  box-sizing: border-box;
  margin-top: 80px;
}

.content {
  padding-left: 120px; /* Same as Introduction section */
  width: 80%;
}

.title {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 50px;
  font-weight: 300;
  color: #fff;
  margin: 0 0 40px 0;
  opacity: 1;
}

.text-columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  margin-bottom: 40px;
  justify-content: space-between;
  width: 690px;
}

.column {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: #ffffffe2;
  opacity: 1;
  width: 98%;
}

.column p {
  margin-bottom: 24px;
}

.column p:last-child {
  margin-bottom: 0;
}

@media screen and (max-width: 1200px) {
  .title {
    font-size: 40px;
    margin: 0 0 30px 0;
  }
}

@media screen and (max-width: 768px) {
  .data-exploration {
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
    margin: 0 0 30px 0;
  }
}
</style> 