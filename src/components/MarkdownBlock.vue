<template>
  <div v-html="markdown.render(source)" />
</template>

<script>
import MarkdownIt from 'markdown-it';
import MarkdownItHighlightjs from "markdown-it-highlightjs";

export default {
  setup() {
    const markdown = new MarkdownIt().use(MarkdownItHighlightjs);
    return {
      markdown,
    };
  },
  props: {
    source: {
      type: String,
      default: '',
    },
  },
};
</script>

<style scoped>
code.hljs {
  background-color: #f3f4f5;
  border-radius: 3px;
  padding: 2px 4px;
}
</style>

<style>
pre {
  overflow-y: scroll;
}
</style>