<template>
  <section class="education-section" ref="sectionRef">
    <div class="section-background"></div>
    <div class="parallax-content">
      <h2 class="section-title on-dark" ref="titleRef">教育经历</h2>
      <div class="timeline">
        <div
          v-for="(item, index) in data"
          :key="item.id"
          class="timeline-item"
          :ref="(el) => (itemRefs[index] = el)"
        >
          <div class="timeline-marker">
            <div class="marker-dot"></div>
          </div>
          <div class="timeline-content card">
            <div class="timeline-header">
              <h3 class="school-name">{{ item.school }}</h3>
              <span class="period">{{ item.period }}</span>
            </div>
            <p class="degree">{{ item.degree }}</p>
            <p class="description">{{ item.description }}</p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const props = defineProps({
  data: {
    type: Array,
    default: () => [],
  },
});

const sectionRef = ref(null);
const titleRef = ref(null);
const itemRefs = ref([]);

let ctx = null;

onMounted(() => {
  // GSAP animations
  ctx = gsap.context(() => {
    // Section title animation
    gsap.from(titleRef.value, {
      scrollTrigger: {
        trigger: sectionRef.value,
        start: "top 80%",
        toggleActions: "play none none reverse",
      },
      opacity: 0,
      y: 50,
      duration: 0.8,
      ease: "power2.out",
    });

    // Timeline items animation
    itemRefs.value.forEach((el, index) => {
      if (el) {
        gsap.from(el, {
          scrollTrigger: {
            trigger: el,
            start: "top 85%",
            toggleActions: "play none none reverse",
          },
          opacity: 0,
          x: index % 2 === 0 ? -100 : 100,
          duration: 0.8,
          delay: index * 0.1,
          ease: "power2.out",
        });
      }
    });
  }, sectionRef.value);
});

onUnmounted(() => {
  if (ctx) ctx.revert();
});
</script>

<style lang="scss" scoped>
@use "@/styles/variables" as *;

.education-section {
  min-height: 100vh;
  padding: 4rem 0;
}

.section-background {
  position: absolute;
  inset: 0;
}

.timeline {
  position: relative;
  max-width: 900px;
  margin: 0 auto;
  padding: 2rem 0;
}

.timeline::before {
  content: "";
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  width: 4px;
  height: 100%;
  background: $gradient-mixed;
  border-radius: 2px;
}

.timeline-item {
  position: relative;
  display: flex;
  margin-bottom: 3rem;
  width: 50%;
  padding-right: 3rem;
}

.timeline-item:nth-child(even) {
  margin-left: 50%;
  padding-right: 0;
  padding-left: 3rem;
}

.timeline-marker {
  position: absolute;
  left: 100%;
  top: 50%;
  transform: translate(-50%, -50%);
  z-index: 1;
}

.timeline-item:nth-child(even) .timeline-marker {
  left: 0;
  transform: translate(-50%, -50%);
}

.marker-dot {
  width: 20px;
  height: 20px;
  background: $gradient-mixed;
  border-radius: 50%;
  border: 4px solid white;
  box-shadow: $shadow-md;
}

.timeline-content {
  flex: 1;
}

.timeline-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 0.75rem;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.school-name {
  font-size: 1.5rem;
  font-weight: 700;
  color: $text-primary;
  margin: 0;
}

.period {
  background: $gradient-mixed;
  color: white;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.875rem;
  font-weight: 500;
}

.degree {
  font-size: 1.1rem;
  color: $primary-dark;
  margin-bottom: 0.75rem;
  font-weight: 500;
}

.description {
  color: $text-secondary;
  line-height: 1.7;
}

@media (max-width: 768px) {
  .timeline::before {
    left: 20px;
  }

  .timeline-item,
  .timeline-item:nth-child(even) {
    width: 100%;
    margin-left: 0;
    padding-left: 50px;
    padding-right: 0;
  }

  .timeline-marker,
  .timeline-item:nth-child(even) .timeline-marker {
    left: 20px;
    right: auto;
    transform: translate(-50%, -50%);
  }

  .timeline-header {
    flex-direction: column;
  }
}
</style>
