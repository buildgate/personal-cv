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
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <rect width="20" height="16" x="2" y="4" rx="2" />
              <path d="m22 7-8.97 5.7a1.94 1.94 0 0 1-2.06 0L2 7" />
            </svg>
            <span>{{ data.email }}</span>
          </a>
          <a :href="`tel:${data.phone}`" class="contact-item">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path
                d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"
              />
            </svg>
            <span>{{ data.phone }}</span>
          </a>
          <span class="contact-item">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path d="M20 10c0 6-8 12-8 12s-8-6-8-12a8 8 0 0 1 16 0Z" />
              <circle cx="12" cy="10" r="3" />
            </svg>
            <span>{{ data.location }}</span>
          </span>
        </div>
        <div class="links" ref="linksRef">
          <a :href="data.website" target="_blank" class="btn btn-primary">
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
              <circle cx="12" cy="12" r="10" />
              <path d="M12 2a14.5 14.5 0 0 0 0 20 14.5 14.5 0 0 0 0-20" />
              <path d="M2 12h20" />
            </svg>
            个人网站
          </a>
          <a :href="data.github" target="_blank" class="btn btn-outline">
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
            GitHub
          </a>
        </div>
        <div class="scroll-indicator">
          <span>向下滚动</span>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <path d="M12 5v14" />
            <path d="m19 12-7 7-7-7" />
          </svg>
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
}

.hero-background {
  position: absolute;
  inset: 0;
}

.gradient-overlay {
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

.hero-content {
  text-align: center;
  color: white;
  padding: 2rem;
}

.name {
  font-size: 3.5rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
  background: linear-gradient(135deg, #fff 0%, $primary-light 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.title {
  font-size: 1.5rem;
  color: $secondary-light;
  margin-bottom: 1.5rem;
  font-weight: 500;
}

.summary {
  max-width: 700px;
  margin: 0 auto 2rem;
  font-size: 1.1rem;
  line-height: 1.8;
  color: rgba(255, 255, 255, 0.8);
}

.contact-info {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: rgba(255, 255, 255, 0.8);
  text-decoration: none;
  transition: color 0.3s ease;

  &:hover {
    color: $secondary-light;
  }
}

.links {
  display: flex;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 3rem;

  .btn-outline {
    border-color: rgba(255, 255, 255, 0.5);
    color: white;

    &:hover {
      background: white;
      color: $bg-dark;
      border-color: white;
    }
  }
}

.scroll-indicator {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
  color: rgba(255, 255, 255, 0.6);
  animation: bounce 2s infinite;
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
  .name {
    font-size: 2.5rem;
  }

  .title {
    font-size: 1.25rem;
  }

  .contact-info {
    flex-direction: column;
    align-items: center;
  }

  .links {
    flex-direction: column;
    align-items: center;
  }
}
</style>
