<template>
  <section class="education-section" ref="sectionRef">
    <div class="section-background"></div>
    <div class="parallax-content">
      <h2 class="section-title on-dark" ref="titleRef">教育经历</h2>
      <div class="education-list">
        <div
          v-for="(item, index) in data"
          :key="item.id"
          class="education-item"
          :ref="(el) => (itemRefs[index] = el)"
        >
          <div class="education-card card">
            <div class="education-header">
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
import { ref, onMounted, onUnmounted, watch, nextTick } from "vue";
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

const initAnimations = async () => {
  if (ctx) {
    ctx.revert();
    ctx = null;
  }

  if (!sectionRef.value) return;

  await nextTick();

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

  ScrollTrigger.refresh();
};

onMounted(() => {
  initAnimations();
});

watch(
  () => props.data,
  () => {
    initAnimations();
  },
  { deep: true },
);

onUnmounted(() => {
  if (ctx) ctx.revert();
});
</script>

<style lang="scss" scoped>
@use "@/styles/variables" as *;

.education-section {
  min-height: 100vh;
  padding: 64px 0;
  > .section-background {
    position: absolute;
    inset: 0;
  }

  > .parallax-content {
    > .education-list {
      max-width: 900px;
      margin: 0 auto;
      padding: 32px 0;
      display: flex;
      flex-direction: column;
      gap: 32px;

      > .education-item {
        position: relative;

        > .education-card {
          padding: 32px;

          > .education-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 12px;
            flex-wrap: wrap;
            gap: 8px;

            > .school-name {
              font-size: 24px;
              font-weight: 700;
              color: $text-primary;
              margin: 0;
            }

            > .period {
              background: $gradient-mixed;
              color: white;
              padding: 4px 12px;
              border-radius: 20px;
              font-size: 14px;
              font-weight: 500;
            }
          }

          > .degree {
            font-size: 17.6px;
            color: $primary-dark;
            margin-bottom: 12px;
            font-weight: 500;
          }

          > .description {
            color: $text-secondary;
            line-height: 1.7;
          }
        }
      }
    }
  }
}

@media (max-width: 768px) {
  .education-section {
    > .parallax-content {
      > .education-list {
        > .education-item {
          > .education-card {
            > .education-header {
              flex-direction: column;
            }
          }
        }
      }
    }
  }
}
</style>
