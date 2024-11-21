<script setup lang="ts">
import TimeLine from './components/TimeLine.vue'
import { ref } from 'vue'

const isDark = ref(false)

const handleYearSelect = (year: number) => {
  console.log('Selected year:', year)
}

const toggleTheme = () => {
  isDark.value = !isDark.value
}
</script>

<template>
  <div class="container" :class="{ 'dark': isDark }">
    <button @click="toggleTheme">切换主题</button>
    <h1>时间线组件</h1>
    <TimeLine 
      :start-year="2000" 
      :end-year="2024"
      @select="handleYearSelect"
    />
  </div>
</template>

<style>
/* 亮色主题变量 */
:root {
  --timeline-line-color: #e0e0e0;
  --timeline-dot-border: #646cff;
  --timeline-dot-bg: #fff;
  --timeline-text-color: #666;
  --timeline-selected-color: #42b883;
}

/* 暗色主题变量 */
.dark {
  --timeline-line-color: #333;
  --timeline-dot-border: #646cff;
  --timeline-dot-bg: #1a1a1a;
  --timeline-text-color: #888;
  --timeline-selected-color: #42b883;
}

.container {
  width: 100%;
  padding: 20px;
}

h1 {
  text-align: center;
  margin-bottom: 40px;
}
</style>
