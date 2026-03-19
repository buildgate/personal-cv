<template>
  <div
    class="scroll-indicator"
    :class="{ hidden: isHidden }"
    aria-hidden="true"
  >
    <img src="@/assets/icons/scroll-down.svg" alt="向下滚动" class="icon" />
    <span class="scroll-text">向下滚动</span>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

const isHidden = ref(false);

const updateVisibility = () => {
  const scrollTop = window.scrollY || document.documentElement.scrollTop;
  const viewportHeight =
    window.innerHeight || document.documentElement.clientHeight;
  const documentHeight = document.documentElement.scrollHeight;
  const isAtBottom = scrollTop + viewportHeight >= documentHeight - 10;

  isHidden.value = isAtBottom;
};

onMounted(() => {
  updateVisibility();
  window.addEventListener("scroll", updateVisibility, { passive: true });
  window.addEventListener("resize", updateVisibility);
});

onUnmounted(() => {
  window.removeEventListener("scroll", updateVisibility);
  window.removeEventListener("resize", updateVisibility);
});
</script>

<style lang="scss" scoped>
.scroll-indicator {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  background: linear-gradient(
    180deg,
    rgba(15, 19, 28, 0),
    rgba(15, 19, 28, 0.65)
  );
  color: rgba(255, 255, 255, 0.85);
  font-size: 14px;
  letter-spacing: 0.4px;
  transition:
    opacity 0.3s ease,
    transform 0.3s ease;
  z-index: 999;
  pointer-events: none;

  &.hidden {
    opacity: 0;
    transform: translateY(10px);
  }

  > .icon {
    width: 20px;
    height: 20px;
  }
}
</style>
