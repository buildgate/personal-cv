<template>
  <section class="projects-section" ref="sectionRef">
    <div class="section-background"></div>
    <div class="parallax-content">
      <h2 class="section-title on-dark" ref="titleRef">个人作品</h2>
      <div class="projects-list">
        <div
          v-for="(item, index) in data"
          :key="item.id"
          class="projects-item"
          :ref="(el) => (itemRefs[index] = el)"
        >
          <div class="projects-card card">
            <div class="projects-header">
              <h3 class="project-name">{{ item.name }}</h3>
            </div>
            <p class="project-description">{{ item.description }}</p>
            <div class="project-image" v-if="getImages(item).length">
              <img
                :src="getImageSrc(item, index)"
                :alt="getImageDescription(item, item.name, index)"
              />
              <div class="project-image-caption">
                {{ getImageDescription(item, item.name, index) }}
              </div>
              <div class="carousel-controls" v-if="getImages(item).length > 1">
                <button
                  type="button"
                  class="carousel-btn"
                  @click.stop="prevImage(index, getImages(item).length)"
                  aria-label="上一张"
                >
                  ‹
                </button>
                <button
                  type="button"
                  class="carousel-btn"
                  @click.stop="nextImage(index, getImages(item).length)"
                  aria-label="下一张"
                >
                  ›
                </button>
              </div>
              <div class="carousel-dots" v-if="getImages(item).length > 1">
                <span
                  v-for="(image, dotIndex) in getImages(item)"
                  :key="`${item.id}-dot-${dotIndex}`"
                  class="dot"
                  :class="{ active: getActiveIndex(index) === dotIndex }"
                  @click.stop="setImage(index, dotIndex)"
                ></span>
              </div>
            </div>
            <div v-if="item.tech && item.tech.length" class="tech-tags">
              <span v-for="tech in item.tech" :key="tech" class="tag">
                {{ tech }}
              </span>
            </div>
            <a
              v-if="item.link"
              :href="item.link"
              target="_blank"
              class="btn btn-primary project-link"
            >
              查看项目
            </a>
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

const activeIndexes = ref({});

const getImages = (item) => {
  if (Array.isArray(item.images)) return item.images;
  if (item.image) return [item.image];
  return [];
};

const getActiveIndex = (cardIndex) => activeIndexes.value[cardIndex] ?? 0;

const setImage = (cardIndex, nextIndex) => {
  activeIndexes.value = {
    ...activeIndexes.value,
    [cardIndex]: nextIndex,
  };
};

const nextImage = (cardIndex, total) => {
  const current = getActiveIndex(cardIndex);
  setImage(cardIndex, (current + 1) % total);
};

const prevImage = (cardIndex, total) => {
  const current = getActiveIndex(cardIndex);
  setImage(cardIndex, (current - 1 + total) % total);
};

const getImageSrc = (item, cardIndex) => {
  const images = getImages(item);
  const image = images[getActiveIndex(cardIndex) % Math.max(images.length, 1)];
  return typeof image === "string" ? image : image?.src;
};

const getImageDescription = (item, fallback, cardIndex) => {
  const images = getImages(item);
  const image = images[getActiveIndex(cardIndex) % Math.max(images.length, 1)];
  if (!image) return fallback;
  return typeof image === "string" ? fallback : image.description || fallback;
};

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

.projects-section {
  min-height: 100vh;
  padding: 64px 0;

  > .section-background {
    position: absolute;
    inset: 0;
  }

  > .parallax-content {
    > .projects-list {
      width: 100%;
      margin: 0 auto;
      padding: 32px 0;
      display: grid;
      grid-template-columns: repeat(4, minmax(0, 1fr));
      gap: 24px;
      max-width: 100%;
      overflow-x: hidden;

      > .projects-item {
        position: relative;

        > .projects-card {
          padding: 32px;

          > .projects-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 12px;
            flex-wrap: wrap;
            gap: 8px;

            > .project-name {
              font-size: 24px;
              font-weight: 700;
              color: $text-primary;
              margin: 0;
            }
          }

          > .project-description {
            color: $text-secondary;
            line-height: 1.7;
            margin-bottom: 16px;
          }

          > .project-image {
            position: relative;
            height: 200px;
            overflow: hidden;
            background: $bg-dark;
            border-radius: 12px;
            margin-bottom: 16px;

            > img {
              width: 100%;
              height: 100%;
              object-fit: cover;
              transition: transform 0.5s ease;
              transform-origin: center;
              display: block;
            }

            &:hover {
              > img {
                transform: scale(1.05);
              }
            }

            > .carousel-controls {
              position: absolute;
              inset: 0;
              display: flex;
              align-items: center;
              justify-content: space-between;
              padding: 0 8px;
              opacity: 0;
              transition: opacity 0.3s ease;
              pointer-events: none;
              z-index: 3;

              > .carousel-btn {
                width: 32px;
                height: 32px;
                border-radius: 50%;
                border: none;
                background: rgba(255, 255, 255, 0.85);
                color: $primary-dark;
                font-size: 19.2px;
                cursor: pointer;
                display: flex;
                align-items: center;
                justify-content: center;
                box-shadow: $shadow-sm;
              }
            }

            > .carousel-dots {
              position: absolute;
              bottom: 8px;
              left: 50%;
              transform: translateX(-50%);
              display: flex;
              gap: 6.4px;
              padding: 4.8px 9.6px;
              background: rgba(0, 0, 0, 0.35);
              border-radius: 999px;
              z-index: 3;

              > .dot {
                width: 8px;
                height: 8px;
                border-radius: 50%;
                background: rgba(255, 255, 255, 0.5);
                cursor: pointer;

                &.active {
                  background: white;
                }
              }
            }

            > .project-image-caption {
              position: absolute;
              bottom: 0;
              left: 0;
              right: 0;
              padding: 7.2px 12px;
              font-size: 13.6px;
              color: white;
              background: linear-gradient(
                180deg,
                rgba(0, 0, 0, 0),
                rgba(0, 0, 0, 0.6)
              );
              text-shadow: 0 2px 6px rgba(0, 0, 0, 0.4);
            }

            &:hover {
              > .carousel-controls {
                opacity: 1;
                pointer-events: auto;
              }
            }
          }

          > .tech-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-bottom: 4px;
          }

          > .project-link {
            margin-top: 12px;
            width: 100%;
            justify-content: center;
            padding: 12px 20px;
            font-size: 14.4px;
            border-radius: 12px;
          }
        }
      }
    }
  }
}

@media (max-width: 1200px) {
  .projects-section {
    > .parallax-content {
      > .projects-list {
        grid-template-columns: repeat(3, minmax(0, 1fr));
      }
    }
  }
}

@media (max-width: 992px) {
  .projects-section {
    > .parallax-content {
      > .projects-list {
        grid-template-columns: repeat(2, minmax(0, 1fr));
      }
    }
  }
}

@media (max-width: 768px) {
  .projects-section {
    > .parallax-content {
      > .projects-list {
        grid-template-columns: 1fr;
        > .projects-item {
          > .projects-card {
            > .projects-header {
              flex-direction: column;
            }
          }
        }
      }
    }
  }
}
</style>
