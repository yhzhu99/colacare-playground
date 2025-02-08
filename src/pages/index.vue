<script setup lang="ts" generic="T extends any, O extends any">
defineOptions({
  name: 'IndexPage',
})
</script>

<template>
  <div class="grid-container">
    <router-link to="/code" class="grid-item grid-item-1 button">
      <div class="content">
        <h2>Code</h2>
        <p>Anonymized code ZIP file</p>
      </div>
    </router-link>
    <router-link to="/prompt" class="grid-item button grid-item-2">
      <div class="content">
        <h2>Prompt Template</h2>
        <p>Complete prompt templates</p>
      </div>
    </router-link>
    <router-link to="/case" class="grid-item button grid-item-4">
      <div class="content">
        <h2>Case Study</h2>
        <p>More case studies</p>
      </div>
    </router-link>
    <!-- <router-link to="/rebuttal" class="grid-item button grid-item-4">
      <div class="content">
        <h2>Rebuttal</h2>
        <p>Detailed version of rebuttal</p>
      </div>
    </router-link> -->
    <router-link to="/questionnaire" class="grid-item button grid-item-5">
      <div class="content">
        <h2>Questionnaire</h2>
        <p>Research survey responses</p>
      </div>
    </router-link>
  </div>
</template>

<style scoped>
.grid-container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: 1fr 1fr;
  gap: 40px;
  padding: 10px;
  justify-items: center;
}

.grid-item {
  width: 400px;
  height: 200px;
  padding: 20px;
  border-radius: 30px;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
  transition: all 0.3s cubic-bezier(.25,.8,.25,1);
}

.content {
  text-align: center;
}

.grid-item-1 {
  grid-area: 1 / 1 / 2 / 2;
  background-color: #ffbcc2;
}

.grid-item-2 {
  grid-area: 1 / 2 / 2 / 3;
  background-color: #8fd4eb;
}

.grid-item-3 {
  grid-area: 1 / 3 / 2 / 4;
  background-color: #aafec2;
}

.grid-item-4 {
  grid-area: 2 / 1 / 3 / 2;
  background-color: #ffdc8b;
}

.grid-item-5 {
  grid-area: 2 / 2 / 3 / 3;
  background-color: #e6c3ff; /* light purple */
}

.button:hover {
  transform: scale(1.05);
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.2);
  cursor: pointer;
}

.content h2 {
  font-family: 'Consolas', monospace;
  font-size: 24px;
  color: #333;
}

.content p {
  font-family: 'Consolas', monospace;
  font-size: 18px;
  color: #666;
}

.content img {
  width: 100%;
  height: auto;
}

@media (max-width: 1400px) {
  .grid-container {
    grid-template-columns: 1fr 1fr;
    grid-template-rows: auto;
  }

  .grid-item-1 { grid-area: 1 / 1 / 2 / 2; }
  .grid-item-2 { grid-area: 1 / 2 / 2 / 3; }
  .grid-item-3 { grid-area: 2 / 1 / 3 / 2; }
  .grid-item-4 { grid-area: 2 / 2 / 3 / 3; }
  .grid-item-5 { grid-area: 3 / 1 / 4 / 2; }
}

@media (max-width: 900px) {
  .grid-container {
    grid-template-columns: 1fr;
  }

  .grid-item {
    width: 100%;
    max-width: 400px;
  }

  .grid-item-1 { grid-area: auto; }
  .grid-item-2 { grid-area: auto; }
  .grid-item-3 { grid-area: auto; }
  .grid-item-4 { grid-area: auto; }
  .grid-item-5 { grid-area: auto; }
}
</style>
