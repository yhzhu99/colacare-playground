<script setup>
import Prompts from '~/assets/prompt_template.md'
</script>

<template>
  <div>
    <div class="markdown-content">
      <Prompts />
    </div>
  </div>
</template>

<style>
.markdown-content {
  line-height: 1.6;
  color: #333;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  text-align: left;
  overflow-wrap: break-word;
  word-wrap: break-word;
}

.markdown-content h1, .markdown-content h2, .markdown-content h3,
.markdown-content h4, .markdown-content h5, .markdown-content h6 {
  margin-top: 24px;
  margin-bottom: 16px;
  font-weight: 600;
  line-height: 1.25;
  text-align: left;
}

.markdown-content h1 { font-size: 2em; border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; }
.markdown-content h2 { font-size: 1.5em; border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; }
.markdown-content h3 { font-size: 1.25em; }
.markdown-content h4 { font-size: 1em; }
.markdown-content h5 { font-size: 0.875em; }
.markdown-content h6 { font-size: 0.85em; color: #6a737d; }

.markdown-content p {
  margin-top: 0;
  margin-bottom: 16px;
  text-align: left;
}

.markdown-content a {
  color: #0366d6;
  text-decoration: none;
}

.markdown-content a:hover {
  text-decoration: underline;
}

.markdown-content code {
  font-family: Consolas, 'Courier New', monospace;
  font-size: 85%;
  background-color: rgba(27,31,35,0.05);
  padding: 0.2em 0.4em;
  border-radius: 3px;
}

.markdown-content pre {
  background-color: #f6f8fa;
  border-radius: 3px;
  font-size: 85%;
  line-height: 1.45;
  overflow: auto;
  padding: 16px;
  text-align: left;
}

.markdown-content pre code {
  background-color: transparent;
  padding: 0;
}

.markdown-content blockquote {
  margin: 0;
  padding: 0 1em;
  color: #6a737d;
  border-left: 0.25em solid #dfe2e5;
}

.markdown-content ul {
  list-style-type: disc;
  padding-left: 2em;
  text-align: left;
}

.markdown-content ol {
  list-style-type: decimal;
  padding-left: 2em;
  text-align: left;
}

.markdown-content ul ul {
  list-style-type: circle;
  padding-left: 2em;
}

.markdown-content ol ol {
  list-style-type: lower-alpha;
  padding-left: 2em;
}

.markdown-content ul ul ul {
  list-style-type: square;
  padding-left: 2em;
}

.markdown-content ol ol ol {
  list-style-type: lower-roman;
  padding-left: 2em;
}

.markdown-content li {
  margin-bottom: 0.5em;
}

.markdown-content img {
  max-width: 100%;
  box-sizing: content-box;
}

.markdown-content hr {
  height: 0.25em;
  padding: 0;
  margin: 24px 0;
  background-color: #e1e4e8;
  border: 0;
}
</style>
