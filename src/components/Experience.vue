<template>
  <section class="experience-section" ref="sectionRef">
    <div class="section-background"></div>
    <div class="parallax-content">
      <h2 class="section-title fade-in on-dark" :class="{ visible: isVisible }">
        工作经历
      </h2>
      <div class="experience-list">
        <div
          v-for="(item, index) in data"
          :key="item.id"
          class="experience-item card"
          :ref="(el) => (itemRefs[index] = el)"
        >
          <div class="experience-header">
            <div class="company-info">
              <h3 class="company-name">{{ item.company }}</h3>
              <p class="position">{{ item.position }}</p>
            </div>
            <span class="period-badge">{{ item.period }}</span>
          </div>
          <p class="description">{{ item.description }}</p>
          <div
            v-if="item.images && item.images.length"
            class="experience-gallery"
          >
            <div
              v-for="(image, imgIndex) in item.images"
              :key="`${item.id}-image-${imgIndex}`"
              class="gallery-item"
            >
              <img
                :src="image"
                :alt="`${item.company} 项目图 ${imgIndex + 1}`"
              />
            </div>
          </div>
          <div class="achievements">
            <h4 class="achievements-title">主要成就</h4>
            <ul class="achievements-list">
              <li v-for="(achievement, i) in item.achievements" :key="i">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="16"
                  height="16"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path
                    d="M12 22c5.523 0 10-4.477 10-10S17.523 2 12 2 2 6.477 2 12s4.477 10 10 10z"
                  />
                  <path d="m9 12 2 2 4-4" />
                </svg>
                <span>{{ achievement }}</span>
              </li>
            </ul>
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
const isVisible = ref(true);
const itemRefs = ref([]);

let ctx = null;

onMounted(() => {
  // GSAP animations
  ctx = gsap.context(() => {
    // Section title animation
    gsap.from(".experience-section .section-title", {
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

    // Experience items animation
    itemRefs.value.forEach((el, index) => {
      if (el) {
        gsap.from(el, {
          scrollTrigger: {
            trigger: el,
            start: "top 85%",
            toggleActions: "play none none reverse",
          },
          opacity: 0,
          x: index % 2 === 0 ? -80 : 80,
          duration: 0.8,
          delay: index * 0.1,
          ease: "power2.out",
        });
      }
    });

    isVisible.value = true;
  }, sectionRef.value);
});

onUnmounted(() => {
  if (ctx) ctx.revert();
});
</script>

<style lang="scss" scoped>
@use "@/styles/variables" as *;

.experience-section {
  min-height: 100vh;
  padding: 4rem 0;
}

.section-background {
  position: absolute;
  inset: 0;
}

.gradient-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background:
    radial-gradient(
      ellipse at 30% 20%,
      rgba($primary-color, 0.15) 0%,
      transparent 50%
    ),
    radial-gradient(
      ellipse at 70% 80%,
      rgba($secondary-color, 0.1) 0%,
      transparent 50%
    );
}

.experience-list {
  max-width: 900px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.experience-item {
  position: relative;
  overflow: hidden;
}

.experience-item::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 4px;
  height: 100%;
  background: $gradient-mixed;
}

.experience-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1rem;
  flex-wrap: wrap;
  gap: 1rem;
}

.company-info {
  flex: 1;
}

.company-name {
  font-size: 1.5rem;
  font-weight: 700;
  color: $text-primary;
  margin: 0 0 0.25rem;
}

.position {
  font-size: 1.1rem;
  color: $primary-dark;
  font-weight: 500;
  margin: 0;
}

.period-badge {
  background: $gradient-primary;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 25px;
  font-size: 0.875rem;
  font-weight: 600;
  white-space: nowrap;
}

.description {
  color: $text-secondary;
  line-height: 1.7;
  margin-bottom: 1.5rem;
}

.experience-gallery {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.gallery-item {
  border-radius: 12px;
  overflow: hidden;
  background: rgba(255, 255, 255, 0.6);
  box-shadow: $shadow-md;
}

.gallery-item img {
  width: 100%;
  height: 140px;
  object-fit: cover;
  display: block;
}

.achievements {
  background: rgba($primary-light, 0.3);
  border-radius: 12px;
  padding: 1.25rem;
}

.achievements-title {
  font-size: 1rem;
  font-weight: 600;
  color: $text-primary;
  margin: 0 0 0.75rem;
}

.achievements-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.achievements-list li {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  color: $text-secondary;
  line-height: 1.5;
}

.achievements-list li svg {
  flex-shrink: 0;
  color: $secondary-color;
  margin-top: 2px;
}

@media (max-width: 768px) {
  .experience-header {
    flex-direction: column;
  }

  .period-badge {
    align-self: flex-start;
  }

  .experience-gallery {
    grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  }

  .gallery-item img {
    height: 120px;
  }
}
</style>
