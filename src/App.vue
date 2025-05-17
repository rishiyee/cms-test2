<script setup>
import { marked } from "marked";
import { onMounted, ref } from "vue";

const markdownHtml = ref("");
const hasValidTextContent = ref(true);

onMounted(() => loadMarkdownFile());

async function loadMarkdownFile() {
  try {
    const response = await fetch("/assets/cmscontent/index.md");
    if (response.ok) {
      const markdownText = await response.text();
      hasValidTextContent.value = !markdownText.toLowerCase().includes("!doctype");
      if (hasValidTextContent.value) {
        markdownHtml.value = marked(markdownText);
      }
    } else {
      hasValidTextContent.value = false;
      console.error("Failed to fetch markdown file:", response.status);
    }
  } catch (error) {
    hasValidTextContent.value = false;
    console.error("Error loading markdown file:", error);
  }
}
</script>

<template>
  <div v-if="!hasValidTextContent">Loading.....</div>
  <div v-else v-html="markdownHtml"></div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
