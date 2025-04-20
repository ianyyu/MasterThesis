<template>
  <section class="equation-section" ref="sectionRef">
    <div class="content">
      <div class="equation-container">
        <div class="equation" ref="equationRef">
          \[
            E(\text{complexity}, \text{tokens}) = \underset{\text{scaling factor}}{\underline{0.001 \times \text{complexity}}} \times \overset{\text{log scale}}{\overline{[5 \times \ln(\text{tokens})]}} + \underset{\text{baseline}}{\underline{0.05}}
          \]
        </div>
        
        <div class="annotations">
          <div class="annotation scaling" ref="scalingAnnotation">
            <div class="annotation-content">
              We need some scaling factor so that the final "energy" values fall into a convenient (small) numeric range. For demonstration purposes, we don't want the energy to be, say, 1,000 or 10,000. We want a smaller, more manageable number in the realm of "0.1" or "0.2."
            </div>
          </div>
          
          <div class="annotation log" ref="logAnnotation">
            <div class="annotation-content">
              We assume that increasing the token count does increase energy consumption but not in a strictly linear way. So we used a scaled log
            </div>
          </div>
          
          <div class="annotation baseline" ref="baselineAnnotation">
            <div class="annotation-content">
              This is simply a baseline or "idle cost." Even if complexity or tokens is very small, you might assume there's some overhead—like a system can't run at zero cost.
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const sectionRef = ref(null);
const equationRef = ref(null);

// Annotation references
const scalingAnnotation = ref(null);
const logAnnotation = ref(null);
const baselineAnnotation = ref(null);

onMounted(() => {
  // Wait for MathJax to be available
  const waitForMathJax = () => {
    if (window.MathJax && window.MathJax.typesetPromise) {
      window.MathJax.typesetPromise([equationRef.value]).then(() => {
        // MathJax has finished rendering
        startObserver();
      });
    } else {
      setTimeout(waitForMathJax, 100);
    }
  };

  waitForMathJax();

  function startObserver() {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          // Sequence the animations
          setTimeout(() => {
            scalingAnnotation.value?.classList.add('animate-in');
          }, 0);

          setTimeout(() => {
            logAnnotation.value?.classList.add('animate-in');
          }, 800);

          setTimeout(() => {
            baselineAnnotation.value?.classList.add('animate-in');
          }, 1600);
        } else {
          // Remove animations when out of view
          [scalingAnnotation, logAnnotation, baselineAnnotation].forEach(ref => {
            ref.value?.classList.remove('animate-in');
          });
        }
      });
    }, {
      threshold: 0.5
    });

    if (sectionRef.value) {
      observer.observe(sectionRef.value);
    }

    onBeforeUnmount(() => {
      if (sectionRef.value) {
        observer.unobserve(sectionRef.value);
      }
    });
  }
});
</script>

<style scoped>
.equation-section {
  position: relative;
  width: 100%;
  min-height: 100vh;
  background: hsla(260,40%,5%,1);
  display: flex;
  align-items: center;
  padding: 40px 0;
  box-sizing: border-box;
}

.content {
  width: 100%;
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 40px;
}

.equation-container {
  position: relative;
  width: 100%;
  padding: 160px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.equation {
  font-size: 32px;
  color: #fff;
  text-align: center;
  margin: 0;
  padding: 0;
  position: relative;
  z-index: 2;
  width: 100%;
}

.annotations {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
  z-index: 1;
}

.annotation {
  position: absolute;
  width: 280px;
}

.annotation-content {
  font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 15px;
  font-weight: 400;
  line-height: 1.5;
  letter-spacing: -0.01em;
  color: rgba(255, 255, 255, 0.8);
  opacity: 0;
  transition: opacity 0.5s ease;
}

.annotation.animate-in .annotation-content {
  opacity: 1;
}

.scaling {
  left: 50px;
  bottom: 40px;
}

.log {
  left: 50%;
  transform: translateX(-50%);
  top: 40px;
}

.baseline {
  right: 50px;
  bottom: 40px;
}

@media screen and (max-width: 1200px) {
  .equation {
    font-size: 28px;
  }
  
  .annotation {
    width: 240px;
  }

  .annotation-content {
    font-size: 14px;
  }
}

@media screen and (max-width: 768px) {
  .equation-section {
    padding: 30px 0;
  }

  .equation {
    font-size: 20px;
  }

  .equation-container {
    padding: 40px 0;
  }

  .annotations {
    position: relative;
    margin-top: 40px;
  }

  .annotation {
    position: relative;
    width: 100%;
    margin: 15px 0;
    left: auto;
    right: auto;
    top: auto;
    bottom: auto;
    transform: none;
  }

  .annotation-content {
    padding: 0 20px;
    font-size: 14px;
  }
}
</style> 