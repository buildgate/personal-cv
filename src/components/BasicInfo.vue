<template>
  <section class="hero-section" ref="sectionRef">
    <div class="hero-background">
      <div class="gradient-overlay"></div>
    </div>
    <div class="parallax-content">
      <div class="hero-content" ref="heroContent">
        <h1 class="name" ref="nameRef">{{ data.name }}</h1>
        <p class="title" ref="titleRef">{{ data.title }}</p>
        <p class="summary" ref="summaryRef">{{ data.summary }}</p>
        <div class="contact-info" ref="contactRef">
          <a :href="`mailto:${data.email}`" class="contact-item">
            <img src="@/assets/icons/mail.svg" alt="邮箱" class="icon" />
            <span>{{ data.email }}</span>
          </a>
          <a :href="`tel:${data.phone}`" class="contact-item">
            <img src="@/assets/icons/phone.svg" alt="电话" class="icon" />
            <span>{{ data.phone }}</span>
          </a>
          <span class="contact-item">
            <img src="@/assets/icons/location.svg" alt="位置" class="icon" />
            <span>{{ data.location }}</span>
          </span>
        </div>
        <div class="links" ref="linksRef">
          <a :href="data.website" target="_blank" class="btn btn-primary">
            <img src="@/assets/icons/globe.svg" alt="个人网站" class="icon" />
            个人网站
          </a>
          <a :href="data.github" target="_blank" class="btn btn-outline">
            <img src="@/assets/icons/github.svg" alt="GitHub" class="icon" />
            GitHub
          </a>
        </div>
        <div class="scroll-indicator">
          <span>向下滚动</span>
          <img
            src="@/assets/icons/scroll-down.svg"
            alt="向下滚动"
            class="icon"
          />
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import gsap from "gsap";

const props = defineProps({
  data: {
    type: Object,
    default: () => ({}),
  },
});

const sectionRef = ref(null);
const heroContent = ref(null);
const nameRef = ref(null);
const titleRef = ref(null);
const summaryRef = ref(null);
const contactRef = ref(null);
const linksRef = ref(null);

let ctx = null;

onMounted(() => {
  // GSAP entrance animations
  ctx = gsap.context(() => {
    const tl = gsap.timeline({ defaults: { ease: "power2.out" } });

    tl.from(nameRef.value, {
      opacity: 0,
      y: 30,
      duration: 0.6,
    })
      .from(
        titleRef.value,
        {
          opacity: 0,
          y: 20,
          duration: 0.5,
        },
        "-=0.3",
      )
      .from(
        summaryRef.value,
        {
          opacity: 0,
          y: 20,
          duration: 0.5,
        },
        "-=0.2",
      )
      .from(
        contactRef.value,
        {
          opacity: 0,
          y: 20,
          duration: 0.5,
        },
        "-=0.2",
      )
      .from(
        linksRef.value,
        {
          opacity: 0,
          y: 20,
          duration: 0.5,
        },
        "-=0.2",
      )
      .from(
        ".scroll-indicator",
        {
          opacity: 0,
          y: 20,
          duration: 0.5,
        },
        "-=0.2",
      );
  }, sectionRef.value);
});

onUnmounted(() => {
  if (ctx) ctx.revert();
});
</script>

<style lang="scss" scoped>
@use "@/styles/variables" as *;

.hero-section {
  min-height: 100vh;
  position: relative;
  > .hero-background {
    position: absolute;
    inset: 0;

    > .gradient-overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: radial-gradient(
        ellipse at center,
        transparent 0%,
        rgba($bg-dark, 0.5) 100%
      );
    }
  }

  > .parallax-content {
    > .hero-content {
      text-align: center;
      color: white;
      padding: 32px;

      > .name {
        font-size: 56px;
        font-weight: 700;
        margin-bottom: 8px;
        background: linear-gradient(135deg, #fff 0%, $primary-light 100%);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
        word-break: break-word;
        overflow-wrap: anywhere;
      }

      > .title {
        font-size: 24px;
        color: $secondary-light;
        margin-bottom: 24px;
        font-weight: 500;
        word-break: break-word;
        overflow-wrap: anywhere;
      }

      > .summary {
        max-width: 700px;
        margin: 0 auto 32px;
        font-size: 16px;
        line-height: 1.8;
        color: rgba(255, 255, 255, 0.8);
        word-break: break-word;
        overflow-wrap: anywhere;
      }

      > .contact-info {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 24px;
        margin-bottom: 32px;
        max-width: 100%;

        > .contact-item {
          display: flex;
          align-items: center;
          gap: 8px;
          color: rgba(255, 255, 255, 0.8);
          text-decoration: none;
          transition: color 0.3s ease;
          max-width: 100%;
          flex-wrap: wrap;
          justify-content: center;
          text-align: center;

          > span {
            word-break: break-word;
            overflow-wrap: anywhere;
          }

          > .icon {
            width: 20px;
            height: 20px;
            flex-shrink: 0;
          }

          &:hover {
            color: $secondary-light;
          }
        }
      }

      > .links {
        display: flex;
        justify-content: center;
        gap: 16px;
        margin-bottom: 48px;
        flex-wrap: wrap;

        > .btn-outline {
          border-color: rgba(255, 255, 255, 0.5);
          color: white;

          &:hover {
            background: white;
            color: $bg-dark;
            border-color: white;
          }
        }

        > .btn {
          > .icon {
            width: 18px;
            height: 18px;
            flex-shrink: 0;
          }
        }
      }

      > .scroll-indicator {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 8px;
        color: rgba(255, 255, 255, 0.6);
        animation: bounce 2s infinite;

        > .icon {
          width: 24px;
          height: 24px;
        }
      }
    }
  }
}

@keyframes bounce {
  0%,
  20%,
  50%,
  80%,
  100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-10px);
  }
  60% {
    transform: translateY(-5px);
  }
}

@media (max-width: 768px) {
  .hero-section {
    > .parallax-content {
      > .hero-content {
        > .name {
          font-size: 40px;
        }

        > .title {
          font-size: 20px;
        }

        > .summary {
          font-size: 15.2px;
        }

        > .contact-info {
          flex-direction: column;
          align-items: center;
        }

        > .links {
          flex-direction: column;
          align-items: center;
        }
      }
    }
  }
}
</style>
