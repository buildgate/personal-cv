<template>
  <section class="projects-section" ref="sectionRef">
    <div class="section-background"></div>
    <div class="parallax-content">
      <h2 class="section-title fade-in on-dark" :class="{ visible: isVisible }">
        个人作品
      </h2>
      <div class="projects-grid">
        <div
          v-for="(item, index) in data"
          :key="item.id"
          class="project-card"
          :ref="(el) => (cardRefs[index] = el)"
        >
          <div class="project-image">
            <img :src="item.image" :alt="item.name" />
            <div class="project-overlay">
              <a :href="item.link" target="_blank" class="btn btn-primary">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="18"
                  height="18"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path
                    d="M15 22v-4a4.8 4.8 0 0 0-1-3.5c3 0 6-2 6-5.5.08-1.25-.27-2.48-1-3.5.28-1.15.28-2.35 0-3.5 0 0-1 0-3 1.5-2.64-.5-5.36-.5-8 0C6 2 5 2 5 2c-.3 1.15-.3 2.35 0 3.5A5.403 5.403 0 0 0 4 9c0 3.5 3 5.5 6 5.5-.39.49-.68 1.05-.85 1.65-.17.6-.22 1.23-.15 1.85v4"
                  />
                  <path d="M9 18c-4.51 2-5-2-7-2" />
                </svg>
                查看项目
              </a>
            </div>
          </div>
          <div class="project-info">
            <h3 class="project-name">{{ item.name }}</h3>
            <p class="project-description">{{ item.description }}</p>
            <div class="tech-tags">
              <span v-for="tech in item.tech" :key="tech" class="tag">
                {{ tech }}
              </span>
            </div>
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
const cardRefs = ref([]);

let ctx = null;

onMounted(() => {
  // GSAP animations
  ctx = gsap.context(() => {
    // Section title animation
    gsap.from(".projects-section .section-title", {
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

    // Project cards stagger animation
    cardRefs.value.forEach((el, index) => {
      if (el) {
        gsap.from(el, {
          scrollTrigger: {
            trigger: el,
            start: "top 85%",
            toggleActions: "play none none reverse",
          },
          opacity: 0,
          y: 60,
          scale: 0.95,
          duration: 0.7,
          delay: index * 0.15,
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

.projects-section {
  min-height: 100vh;
  padding: 4rem 0;
  position: relative;
}

.section-background {
  position: absolute;
  inset: 0;
  z-index: 0;
}

.mesh-gradient {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
}

.projects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 2rem;
  max-width: 1200px;
  margin: 0 auto;
  position: relative;
  z-index: 1;
}

.project-card {
  background: $card-bg;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: $shadow-lg;
  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease;

  &:hover {
    transform: translateY(-10px);
    box-shadow: $shadow-xl;
  }
}

.project-image {
  position: relative;
  height: 200px;
  overflow: hidden;
}

.project-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.project-card:hover .project-image img {
  transform: scale(1.1);
}

.project-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba($bg-dark, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.project-card:hover .project-overlay {
  opacity: 1;
}

.project-info {
  padding: 1.5rem;
}

.project-name {
  font-size: 1.25rem;
  font-weight: 700;
  color: $text-primary;
  margin: 0 0 0.75rem;
}

.project-description {
  color: $text-secondary;
  line-height: 1.6;
  margin-bottom: 1rem;
}

.tech-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

@media (max-width: 768px) {
  .projects-grid {
    grid-template-columns: 1fr;
    padding: 0 1rem;
  }
}
</style>
