<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'

interface TimeLineProps {
  startYear: number
  endYear: number
}

const props = defineProps<TimeLineProps>()
const emit = defineEmits<{
  (e: 'select', year: number): void
}>()

const timelineRef = ref<HTMLDivElement>()
const isDragging = ref(false)
const startX = ref(0)
const scrollLeft = ref(0)
const selectedYear = ref<number | null>(null)

const years = computed(() => {
  const result = []
  for (let year = props.startYear; year <= props.endYear; year++) {
    result.push(year)
  }
  return result
})

const handleMouseDown = (e: MouseEvent) => {
  isDragging.value = true
  startX.value = e.pageX - timelineRef.value!.offsetLeft
  scrollLeft.value = timelineRef.value!.scrollLeft
}

const handleMouseMove = (e: MouseEvent) => {
  if (!isDragging.value) return
  
  const x = e.pageX - timelineRef.value!.offsetLeft
  const walk = (x - startX.value) * 2
  timelineRef.value!.scrollLeft = scrollLeft.value - walk
}

const handleMouseUp = () => {
  isDragging.value = false
}

const selectYear = (year: number) => {
  selectedYear.value = year
  emit('select', year)
}

onMounted(() => {
  timelineRef.value?.addEventListener('mouseleave', handleMouseUp)
})
</script>

<template>
  <div 
    ref="timelineRef"
    class="timeline-container"
    @mousedown="handleMouseDown"
    @mousemove="handleMouseMove"
    @mouseup="handleMouseUp"
  >
    <div class="timeline">
      <div class="timeline-line"></div>
      <div 
        v-for="year in years" 
        :key="year"
        class="year-item"
        :class="{ 'selected': selectedYear === year }"
        @click="selectYear(year)"
      >
        <div class="year-mark"></div>
        <div class="year-label">{{ year }}</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* 定义颜色变量 */
:root {
  --timeline-line-color: #e0e0e0;
  --timeline-dot-border: #646cff;
  --timeline-dot-bg: #fff;
  --timeline-text-color: #666;
  --timeline-selected-color: #42b883;
}

/* 暗色模式 */
@media (prefers-color-scheme: dark) {
  :root {
    --timeline-line-color: #333;
    --timeline-dot-border: #646cff;
    --timeline-dot-bg: #1a1a1a;
    --timeline-text-color: #888;
    --timeline-selected-color: #42b883;
  }
}

.timeline-container {
  width: 100%;
  overflow-x: auto;
  cursor: grab;
  user-select: none;
  padding: 20px 0;
}

.timeline-container:active {
  cursor: grabbing;
}

.timeline {
  display: flex;
  align-items: center;
  min-width: max-content;
  padding: 0 20px;
  position: relative;
}

.timeline-line {
  position: absolute;
  top: 6px;
  left: 0;
  right: 0;
  height: 2px;
  background-color: var(--timeline-line-color);
  z-index: 0;
}

.year-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0 40px;
  cursor: pointer;
}

.year-mark {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background-color: var(--timeline-dot-bg);
  border: 2px solid var(--timeline-dot-border);
  margin-bottom: 16px;
  position: relative;
  z-index: 1;
  transition: all 0.3s ease;
  transform: translateY(0);
}

.year-label {
  font-size: 14px;
  color: var(--timeline-text-color);
}

.year-item:hover .year-label {
  color: var(--timeline-dot-border);
}

.year-item:hover .year-mark {
  transform: scale(1.2);
}

.year-item.selected .year-mark {
  width: 16px;
  height: 16px;
  transform: translateY(-2px);
}

.year-item.selected .year-label {
  color: var(--timeline-selected-color);
  font-weight: bold;
}
</style> 