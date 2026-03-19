<template>
  <div class="resume-app" ref="appRef">
    <div class="background-layer" ref="bgLayer"></div>
    <BasicInfo :data="resumeData.basicInfo" />
    <Education :data="resumeData.education" />
    <Experience :data="resumeData.experience" />
    <Projects :data="resumeData.projects" />
    <footer class="footer">
      <p>
        © {{ currentYear }} {{ resumeData.basicInfo.name }}. All rights
        reserved.
      </p>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import BasicInfo from "./components/BasicInfo.vue";
import Education from "./components/Education.vue";
import Experience from "./components/Experience.vue";
import Projects from "./components/Projects.vue";

const resumeData = ref({
  basicInfo: {},
  education: [],
  experience: [],
  projects: [],
});

const currentYear = new Date().getFullYear();
const appRef = ref(null);

onMounted(async () => {
  try {
    const response = await fetch(`/resume-data.json?t=${Date.now()}`, {
      cache: "no-store",
    });
    const data = await response.json();
    resumeData.value = data;

    // Wait for DOM to update
    await new Promise((resolve) => setTimeout(resolve, 100));
  } catch (error) {
    console.error("Failed to load resume data:", error);
  }
});
</script>

<style lang="scss" scoped>
@use "@/styles/variables" as *;

.resume-app {
  width: 100%;
  position: relative;

  > .background-layer {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: $bg-dark;
    z-index: -1;
    pointer-events: none;
  }

  > .footer {
    background: $bg-dark;
    color: $text-light;
    text-align: center;
    padding: 32px;
    font-size: 14px;
  }
}
</style>
