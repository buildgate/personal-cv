<template>
  <div class="resume-app" ref="appRef">
    <div class="background-layer" ref="bgLayer"></div>
    <div class="export-wrapper">
      <button
        class="export-btn"
        type="button"
        @click="exportPdf"
        title="导出 PDF"
      >
        <img src="@/assets/icons/download.svg" alt="导出" class="btn-icon" />
      </button>
      <button
        class="preview-btn"
        type="button"
        @click="togglePreview"
        title="预览"
      >
        <img src="@/assets/icons/preview.svg" alt="预览" class="btn-icon" />
      </button>
    </div>
    <BasicInfo :data="resumeData.basicInfo" />
    <Education :data="resumeData.education" />
    <Experience :data="resumeData.experience" />
    <Projects :data="resumeData.projects" />
    <ScrollIndicator />
    <div class="preview-drawer" :class="{ open: isPreviewOpen }">
      <div class="drawer-header">
        <span>导出预览</span>
        <button type="button" class="drawer-close" @click="togglePreview">
          关闭
        </button>
      </div>
      <div class="drawer-content">
        <div class="preview-frame">
          <iframe :src="previewUrl" title="PDF 预览" />
        </div>
      </div>
    </div>
    <footer class="footer">
      <p>
        © {{ currentYear }} {{ resumeData.basicInfo.name }}. All rights
        reserved.
      </p>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { jsPDF } from "jspdf";
import BasicInfo from "./components/BasicInfo.vue";
import Education from "./components/Education.vue";
import Experience from "./components/Experience.vue";
import Projects from "./components/Projects.vue";
import ScrollIndicator from "./components/ScrollIndicator.vue";

const resumeData = ref({
  basicInfo: {},
  education: [],
  experience: [],
  projects: [],
});

const currentYear = new Date().getFullYear();
const appRef = ref(null);
const isPreviewOpen = ref(false);
const previewUrl = ref("");

const generatePdf = async () => {
  const data = resumeData.value;
  const doc = new jsPDF({ unit: "pt", format: "a4" });

  const fontUrl = "/fonts/SourceHanSansCN-VF.ttf";
  const fontResponse = await fetch(fontUrl);
  const fontBuffer = await fontResponse.arrayBuffer();
  const fontBytes = new Uint8Array(fontBuffer);
  const chunkSize = 0x8000;
  let fontBinary = "";
  for (let i = 0; i < fontBytes.length; i += chunkSize) {
    fontBinary += String.fromCharCode(...fontBytes.subarray(i, i + chunkSize));
  }
  const fontBase64 = btoa(fontBinary);

  doc.addFileToVFS("SourceHanSansCN.ttf", fontBase64);
  doc.addFont("SourceHanSansCN.ttf", "SourceHanSansCN", "normal");
  doc.setFont("SourceHanSansCN", "normal");

  const pageWidth = doc.internal.pageSize.getWidth();
  const pageHeight = doc.internal.pageSize.getHeight();
  const margin = 40;
  const maxWidth = pageWidth - margin * 2;
  let cursorY = margin;

  const addSectionTitle = (text) => {
    doc.setFontSize(15);
    doc.setTextColor(15, 23, 42);
    doc.setFont("SourceHanSansCN", "normal");
    doc.text(text, margin, cursorY);
    cursorY += 18;
    doc.setDrawColor(220, 224, 230);
    doc.line(margin, cursorY, pageWidth - margin, cursorY);
    cursorY += 14;
  };

  const addParagraph = (text, fontSize = 11, spacing = 14) => {
    doc.setFontSize(fontSize);
    doc.setTextColor(55, 65, 81);
    const lines = doc.splitTextToSize(text, maxWidth);
    lines.forEach((line) => {
      if (cursorY + spacing > pageHeight - margin) {
        doc.addPage();
        cursorY = margin;
      }
      doc.text(line, margin, cursorY);
      cursorY += spacing;
    });
    cursorY += 4;
  };

  const addDivider = () => {
    cursorY += 6;
    doc.setDrawColor(233, 236, 240);
    doc.line(margin, cursorY, pageWidth - margin, cursorY);
    cursorY += 18;
  };

  if (data.basicInfo) {
    doc.setFontSize(22);
    doc.setTextColor(15, 23, 42);
    doc.setFont("SourceHanSansCN", "normal");
    doc.text(data.basicInfo.name || "", margin, cursorY);
    cursorY += 26;

    if (data.basicInfo.title) {
      addParagraph(data.basicInfo.title, 12.5, 18);
    }

    const contactParts = [
      data.basicInfo.email,
      data.basicInfo.phone,
      data.basicInfo.location,
      data.basicInfo.website,
      data.basicInfo.github,
    ].filter(Boolean);

    if (contactParts.length) {
      addParagraph(contactParts.join(" · "), 10.5, 16);
    }

    if (data.basicInfo.summary) {
      addParagraph(data.basicInfo.summary, 11, 16);
    }

    addDivider();
  }

  if (Array.isArray(data.education) && data.education.length) {
    addSectionTitle("教育经历");
    data.education.forEach((item) => {
      addParagraph(
        `${item.school || ""} | ${item.degree || ""} | ${item.period || ""}`,
        11,
        16,
      );
      if (item.description) addParagraph(item.description, 10.5, 15);
    });
    addDivider();
  }

  if (Array.isArray(data.experience) && data.experience.length) {
    addSectionTitle("工作经历");
    data.experience.forEach((item) => {
      addParagraph(
        `${item.company || ""} | ${item.position || ""} | ${item.period || ""}`,
        11,
        16,
      );
      if (item.description) addParagraph(item.description, 10.5, 15);
      if (Array.isArray(item.achievements) && item.achievements.length) {
        addParagraph(`亮点：${item.achievements.join("；")}`, 10.5, 15);
      }
    });
    addDivider();
  }

  if (Array.isArray(data.projects) && data.projects.length) {
    addSectionTitle("个人项目");
    data.projects.forEach((item) => {
      const tech = Array.isArray(item.tech) ? item.tech.join(" / ") : "";
      addParagraph(`${item.name || ""}${tech ? ` | ${tech}` : ""}`, 11, 16);
      if (item.description) addParagraph(item.description, 10.5, 15);
    });
  }

  return doc;
};

const updatePreview = async () => {
  const doc = await generatePdf();
  const blob = doc.output("blob");
  if (previewUrl.value) URL.revokeObjectURL(previewUrl.value);
  previewUrl.value = URL.createObjectURL(blob);
};

const togglePreview = async () => {
  if (!isPreviewOpen.value) {
    await updatePreview();
  }
  isPreviewOpen.value = !isPreviewOpen.value;
};

const exportPdf = async () => {
  const doc = await generatePdf();
  doc.save("resume.pdf");
};

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

  > .export-wrapper {
    position: fixed;
    top: 20px;
    right: 24px;
    z-index: 1000;
    display: flex;
    align-items: center;
    gap: 8px;

    > .export-btn,
    > .preview-btn {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 36px;
      height: 36px;
      padding: 0;
      border-radius: 8px;
      background: linear-gradient(
        135deg,
        $primary-color 0%,
        darken($primary-color, 15%) 100%
      );
      border: none;
      cursor: pointer;
      transition: all 0.3s ease;

      > .btn-icon {
        width: 18px;
        height: 18px;
        color: white;
      }

      &:hover {
        transform: translateY(-2px);
        box-shadow: 0 4px 12px rgba($primary-color, 0.4);
      }
    }

    // > .preview-btn {
    //   display: none;
    // }

    > .export-btn:hover,
    > .preview-btn:hover {
      transform: translateY(-2px);
      box-shadow: $shadow-lg;
    }

    > .preview-popover {
      position: absolute;
      top: calc(100% + 12px);
      right: 0;
      width: 320px;
      padding: 16px;
      border-radius: 16px;
      background: rgba(255, 255, 255, 0.95);
      box-shadow: $shadow-xl;
      color: #1f2937;
      backdrop-filter: blur(12px);
      border: 1px solid rgba(255, 255, 255, 0.6);
      animation: fadeIn 0.2s ease;

      .preview-header {
        font-weight: 600;
        margin-bottom: 12px;
        font-size: 14px;
        color: #111827;
      }

      .preview-frame {
        width: 100%;
        height: 360px;
        border-radius: 12px;
        overflow: hidden;
        border: 1px solid #e5e7eb;

        iframe {
          width: 100%;
          height: 100%;
          border: none;
        }
      }
    }
  }

  > .preview-drawer {
    position: fixed;
    left: 0;
    right: 0;
    bottom: -100%;
    background: white;
    z-index: 1000;
    border-radius: 20px 20px 0 0;
    box-shadow: 0 -12px 30px rgba(15, 23, 42, 0.2);
    transition: bottom 0.3s ease;
    max-height: 70vh;
    display: flex;
    flex-direction: column;

    &.open {
      bottom: 0;
    }

    .drawer-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 16px 20px;
      font-weight: 600;
      border-bottom: 1px solid #e5e7eb;
    }

    .drawer-close {
      background: transparent;
      border: none;
      color: $primary-dark;
      font-weight: 600;
      cursor: pointer;
    }

    .drawer-content {
      padding: 16px 20px 24px;
      overflow-y: auto;
      overflow-x: hidden;
      color: #1f2937;

      /* 隐藏抽屉内容区域的滚动条，但保持滚动功能 */
      scrollbar-width: none;
      -ms-overflow-style: none;
      &::-webkit-scrollbar {
        display: none;
      }

      .preview-frame {
        width: 100%;
        height: 60vh;
        border-radius: 12px;
        overflow: hidden;
        border: 1px solid #e5e7eb;

        iframe {
          width: 100%;
          height: 100%;
          border: none;
          /* 隐藏 iframe 的滚动条 */
          overflow: hidden;
        }
      }
    }
  }

  > .footer {
    background: $bg-dark;
    color: $text-light;
    text-align: center;
    padding: 32px;
    font-size: 14px;
  }
}

@media (max-width: 768px) {
  .resume-app {
    > .export-wrapper {
      right: 16px;

      > .preview-btn {
        display: inline-flex;
      }
    }

    > .export-wrapper:hover .preview-popover {
      display: none;
    }
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-6px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
