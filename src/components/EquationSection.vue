<template>
  <section class="equation-section" ref="sectionRef">
    <div class="content">
      <div class="equation-container">
        <div class="equation" ref="equationRef">
          <span class="eq-part">\( E(\text{complexity}, \text{tokens}) = \)</span>
          <div class="term-wrapper scaling-term" ref="scalingWrapper">
            <span class="eq-part" ref="scalingTerm">\( 0.001 \times \text{complexity} \)</span>
            <div class="annotation scaling" ref="scalingAnnotation">
              <div class="annotation-content">
                We need some scaling factor so that the final "energy" values fall into a convenient (small) numeric range. For demonstration purposes, we don't want the energy to be, say, 1,000 or 10,000. We want a smaller, more manageable number in the realm of "0.1" or "0.2."
              </div>
            </div>
          </div>
          <span class="eq-part">\( \times \)</span>
          <div class="term-wrapper log-term" ref="logWrapper">
            <span class="eq-part" ref="logTerm">\( [5 \times \ln(\text{tokens})] \)</span>
            <div class="annotation log" ref="logAnnotation">
              <div class="annotation-content">
                We assume that increasing the token count does increase energy consumption but not in a strictly linear way. So we used a scaled log
              </div>
            </div>
          </div>
          <span class="eq-part">\( + \)</span>
          <div class="term-wrapper baseline-term" ref="baselineWrapper">
            <span class="eq-part" ref="baselineTerm">\( 0.05 \)</span>
            <div class="annotation baseline" ref="baselineAnnotation">
              <div class="annotation-content">
                This is simply a baseline or "idle cost." Even if complexity or tokens is very small, you might assume there's some overhead—like a system can't run at zero cost.
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { annotate } from 'rough-notation';

const sectionRef = ref(null);
const equationRef = ref(null);
const scalingTerm = ref(null);
const logTerm = ref(null);
const baselineTerm = ref(null);
const scalingAnnotation = ref(null);
const logAnnotation = ref(null);
const baselineAnnotation = ref(null);

let annotations = [];

onMounted(() => {
  // Wait for MathJax to be available and render
  const waitForMathJax = () => {
    if (window.MathJax && window.MathJax.typesetPromise) {
      window.MathJax.typesetPromise([equationRef.value]).then(() => {
        // Give MathJax a moment to finish DOM updates
        setTimeout(setupAnnotations, 500);
      }).catch(err => {
        console.error('MathJax typesetting failed:', err);
      });
    } else {
      setTimeout(waitForMathJax, 100);
    }
  };

  waitForMathJax();

  function setupAnnotations() {
    // Find the actual rendered MathJax elements
    const scalingElement = scalingTerm.value?.querySelector('.MathJax');
    const logElement = logTerm.value?.querySelector('.MathJax');
    const baselineElement = baselineTerm.value?.querySelector('.MathJax');

    if (!scalingElement || !logElement || !baselineElement) {
      console.warn('MathJax elements not found, retrying...');
      setTimeout(setupAnnotations, 500);
      return;
    }

    // Create Rough Notation annotations
    const scaling = annotate(scalingElement, { 
      type: 'underline',
      color: 'rgba(255, 255, 255, 0.6)',
      strokeWidth: 2,
      animationDuration: 800,
      padding: 5
    });

    const log = annotate(logElement, { 
      type: 'underline',
      color: 'rgba(255, 255, 255, 0.6)',
      strokeWidth: 2,
      animationDuration: 800,
      padding: 5
    });

    const baseline = annotate(baselineElement, { 
      type: 'circle',
      color: 'rgba(255, 255, 255, 0.6)',
      strokeWidth: 2,
      animationDuration: 800,
      padding: 5
    });

    annotations = [scaling, log, baseline];

    // Create intersection observer for scroll-based animation
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          // Show annotations sequentially
          setTimeout(() => {
            scaling.show();
            scalingAnnotation.value?.classList.add('show');
          }, 0);
          
          setTimeout(() => {
            log.show();
            logAnnotation.value?.classList.add('show');
          }, 800);
          
          setTimeout(() => {
            baseline.show();
            baselineAnnotation.value?.classList.add('show');
          }, 1600);
        } else {
          // Hide all annotations
          annotations.forEach(a => a.hide());
          [scalingAnnotation, logAnnotation, baselineAnnotation].forEach(ref => {
            ref.value?.classList.remove('show');
          });
        }
      });
    }, {
      threshold: 0.5
    });

    observer.observe(equationRef.value);
  }
});

onBeforeUnmount(() => {
  observer.disconnect();
  annotations.forEach(a => a.remove());
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
  padding: 120px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.equation {
  font-size: 32px;
  color: #fff;
  text-align: center;
  margin: 0;
  padding: 20px 0;
  position: relative;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 4px;
}

.term-wrapper {
  position: relative;
  display: inline-flex;
  align-items: center;
}

.eq-part {
  display: inline-flex;
  align-items: center;
}

.term {
  opacity: 0;
  position: absolute;
  pointer-events: none;
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
  opacity: 0;
  transition: opacity 0.5s ease;
  left: 50%;
  transform: translateX(-50%);
  pointer-events: none;
}

.scaling-term .annotation {
  top: calc(100% + 40px);
}

.log-term .annotation {
  bottom: calc(100% + 40px);
}

.baseline-term .annotation {
  top: calc(100% + 40px);
}

.annotation.show {
  opacity: 1;
}

.annotation-content {
  font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 14px;
  font-weight: 400;
  line-height: 1.5;
  letter-spacing: -0.01em;
  
  line-height: 1.5;
  letter-spacing: -0.01em;
  color: rgba(255, 255, 255, 0.4);
  position: relative;
  padding: 16px;
  border-radius: 8px;
}

.annotation-content::before {
  content: '';
  position: absolute;
  width: 2px;
  background: rgba(255, 255, 255, 0.3);
  height: 40px;
}

.scaling-term .annotation-content::before {
  top: -40px;
  left: 50%;
  transform: translateX(-50%);
}

.log-term .annotation-content::before {
  bottom: -40px;
  left: 50%;
  transform: translateX(-50%);
}

.baseline-term .annotation-content::before {
  top: -40px;
  left: 50%;
  transform: translateX(-50%);
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
    flex-direction: column;
    gap: 20px;
  }

  .term-wrapper {
    margin: 10px 0;
  }

  .annotation {
    position: relative;
    width: 100%;
    margin: 15px 0;
    transform: none;
    left: 0;
    opacity: 1;
  }

  .scaling-term .annotation,
  .log-term .annotation,
  .baseline-term .annotation {
    top: auto;
    bottom: auto;
    margin-top: 20px;
  }

  .annotation-content::before {
    display: none;
  }
}
</style> 