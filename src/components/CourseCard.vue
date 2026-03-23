<script setup>
import { computed } from 'vue';

const props = defineProps({
  course: Object,
  status: String,
  activeMode: String
});
</script>

<template>
  <div 
    class="course-card"
    :class="[`status-${status}`, `mode-${activeMode.replace(' ', '-').toLowerCase()}`]"
  >
    <div class="course-code">{{ course.id }}</div>
    <div class="course-name">{{ course.nombre }}</div>
  </div>
</template>

<style scoped>
.course-card {
  background: white;
  border: 1px solid var(--color-gray-medium);
  border-radius: 8px;
  padding: 0.75rem 0.5rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  height: 90px;
  cursor: pointer;
  transition: all var(--transition-fast);
  box-shadow: 0 2px 4px rgba(0,0,0,0.02);
  user-select: none;
  font-size: 0.85rem;
  overflow: hidden;
}

.course-code {
  font-size: 0.7rem;
  color: var(--text-secondary);
  margin-bottom: 0.25rem;
  font-weight: 500;
  transition: color var(--transition-fast);
}

.course-name {
  font-weight: 600;
  color: var(--text-primary);
  line-height: 1.2;
  transition: color var(--transition-fast);
  display: -webkit-box;
  -webkit-line-clamp: 3;
  line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.mode-ver-malla:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);
  border-color: var(--color-gray-dark);
}

/* Hover Map Colors */
.status-hovered {
  background: var(--color-orange);
  border-color: var(--color-orange);
  transform: scale(1.05);
  box-shadow: 0 4px 15px rgba(234, 118, 0, 0.3);
  z-index: 2;
  position: relative;
}
.status-hovered .course-name, .status-hovered .course-code {
  color: var(--text-inverse);
}

.status-prerequisite {
  background: rgba(140, 71, 153, 0.15); /* Purple light */
  border-color: var(--color-purple);
  border-width: 2px;
}
.status-prerequisite .course-name {
  color: var(--color-purple);
}
.status-prerequisite .course-code {
  color: var(--color-purple);
}

.status-opened {
  background: rgba(73, 139, 202, 0.15); /* Blue light */
  border-color: var(--color-blue-light);
  border-width: 2px;
}
.status-opened .course-name {
  color: var(--color-blue-light);
}
.status-opened .course-code {
  color: var(--color-blue-light);
}

.status-dimmed {
  opacity: 0.4;
  filter: grayscale(0.5);
}
.mode-malla-simulada:active {
  transform: scale(0.95);
}

.status-approved {
  background: var(--color-gray-dark);
  border-color: var(--color-gray-dark);
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.2);
}
.status-approved .course-name, .status-approved .course-code {
  color: var(--text-inverse);
  text-decoration: line-through;
  opacity: 0.7;
}

.status-available {
  background: white;
  border-color: var(--color-green);
  border-width: 2px;
  box-shadow: 0 0 10px rgba(0, 164, 153, 0.15);
}
.status-available .course-name {
  color: var(--color-green);
}

.status-locked {
  background: var(--color-gray-light);
  border-color: var(--color-gray-medium);
  opacity: 0.6;
  cursor: not-allowed;
}
.status-locked .course-name {
  color: var(--text-secondary);
}
</style>
