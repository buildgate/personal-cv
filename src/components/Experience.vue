<template>
  <section class="experience-section" ref="sectionRef">
    <div class="section-background"></div>
    <div class="parallax-content">
      <h2 class="section-title on-dark" ref="titleRef">工作经历</h2>
      <div class="experience-list">
        <div
          v-for="(item, index) in data"
          :key="item.id"
          class="experience-item"
          :ref="(el) => (itemRefs[index] = el)"
        >
          <div class="experience-card card">
            <div class="experience-header">
              <h3 class="company-name">{{ item.company }}</h3>
              <span class="period">{{ item.period }}</span>
            </div>
            <p class="position">{{ item.position }}</p>
            <p class="description">{{ item.description }}</p>
            <div
              v-if="item.images && item.images.length"
              class="experience-gallery"
            >
              <div
                v-for="(image, imgIndex) in getVisibleImages(item)"
                :key="`${item.id}-image-${imgIndex}`"
                class="gallery-item"
              >
                <img
                  :src="getImageSrc(image)"
                  :alt="getImageDescription(image, item.company)"
                />
                <p class="gallery-caption">
                  {{ getImageDescription(image, item.company) }}
                </p>
              </div>
            </div>
            <button
              v-if="item.images && item.images.length > 3"
              type="button"
              class="gallery-toggle"
              @click="toggleGallery(item.id)"
            >
              {{ isExpanded(item.id) ? "收起" : "展开更多" }}
            </button>
            <ul
              v-if="item.achievements && item.achievements.length"
              class="achievements-list"
            >
              <li v-for="(achievement, i) in item.achievements" :key="i">
                <img
                  src="@/assets/icons/check-circle.svg"
                  alt="成就"
                  class="icon"
                />
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
const expandedGalleries = ref(new Set());

const getImageSrc = (image) => (typeof image === "string" ? image : image?.src);

const getImageDescription = (image, fallback) =>
  typeof image === "string" ? fallback : image?.description || fallback;

const isExpanded = (id) => expandedGalleries.value.has(id);

const toggleGallery = (id) => {
  const next = new Set(expandedGalleries.value);
  if (next.has(id)) {
    next.delete(id);
  } else {
    next.add(id);
  }
  expandedGalleries.value = next;
};

const getVisibleImages = (item) =>
  isExpanded(item.id) ? item.images : item.images.slice(0, 3);

let ctx = null;

const initAnimations = async () => {
  if (ctx) {
    ctx.revert();
    ctx = null;
  }

  if (!sectionRef.value) return;

  await nextTick();

  ctx = gsap.context(() => {
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

.experience-section {
  min-height: 100vh;
  padding: 64px 0;

  > .section-background {
    position: absolute;
    inset: 0;
  }

  > .parallax-content {
    > .experience-list {
      max-width: 900px;
      margin: 0 auto;
      padding: 32px 0;
      display: flex;
      flex-direction: column;
      gap: 32px;

      > .experience-item {
        position: relative;

        > .experience-card {
          padding: 32px;

          > .experience-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 12px;
            flex-wrap: wrap;
            gap: 8px;

            > .company-name {
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
              white-space: nowrap;
            }
          }

          > .position {
            font-size: 17.6px;
            color: $primary-dark;
            margin-bottom: 12px;
            font-weight: 500;
          }

          > .description {
            color: $text-secondary;
            line-height: 1.7;
            margin-bottom: 16px;
          }

          > .experience-gallery {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 16px;
            margin-bottom: 16px;
            max-width: 100%;
            overflow-x: hidden;

            > .gallery-item {
              border-radius: 12px;
              overflow: hidden;
              background: rgba(255, 255, 255, 0.6);
              box-shadow: $shadow-md;

              > img {
                width: 100%;
                height: 140px;
                object-fit: cover;
                display: block;
                transition: transform 0.5s ease;
                transform-origin: center;
              }

              &:hover {
                > img {
                  transform: scale(1.05);
                }
              }

              > .gallery-caption {
                padding: 8px 12px 12px;
                font-size: 14.4px;
                color: $text-secondary;
                line-height: 1.4;
                background: rgba(255, 255, 255, 0.8);
              }
            }
          }

          > .gallery-toggle {
            align-self: flex-start;
            margin: 0 0 16px;
            padding: 6.4px 16px;
            border-radius: 999px;
            border: 1px solid rgba(0, 0, 0, 0.1);
            background: white;
            color: $primary-dark;
            font-size: 14px;
            cursor: pointer;
            transition: all 0.3s ease;

            &:hover {
              border-color: rgba($primary-color, 0.5);
              color: $primary-color;
              box-shadow: $shadow-sm;
            }
          }

          > .achievements-list {
            list-style: none;
            padding: 0;
            margin: 0;
            display: flex;
            flex-direction: column;
            gap: 12px;

            > li {
              display: flex;
              align-items: flex-start;
              gap: 12px;
              color: $text-secondary;
              line-height: 1.5;

              > .icon {
                flex-shrink: 0;
                width: 16px;
                height: 16px;
                margin-top: 2px;
              }
            }
          }
        }
      }
    }
  }
}

@media (max-width: 768px) {
  .experience-section {
    > .parallax-content {
      > .experience-list {
        > .experience-item {
          > .experience-card {
            > .experience-header {
              flex-direction: column;
            }
          }
        }
      }
    }
  }
}
</style>
