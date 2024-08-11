<script setup>
import { Download } from '@element-plus/icons-vue'
</script>

<template>
  <div class="mx-auto px-4 py-8 container">
    <div class="mx-auto max-w-2xl overflow-hidden rounded-lg bg-white shadow-lg">
      <div class="p-6">
        <h2 class="mb-6 text-center text-2xl font-bold">
          Code Download
        </h2>

        <div class="mb-6 flex justify-center">
          <a href="ColaCare-Code.zip" download>
            <el-button type="primary" :icon="Download" class="flex items-center px-6 py-3">
              <span class="ml-2 text-lg">Download Code</span>
            </el-button>
          </a>
        </div>

        <p class="text-center text-gray-600">
          Currently, the code is anonymized through downloading the ZIP file.
          Later, we will include the publicly available GitHub link.
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.el-button {
  transition: all 0.3s ease;
}

.el-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
</style>
