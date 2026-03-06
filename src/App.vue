<script setup>
import { ref, computed } from 'vue';
import TopBar from './components/TopBar.vue';
import MallaGrid from './components/MallaGrid.vue';
import mallaData from '../data.json';

// Mapa de carreras
const careers = [mallaData];

// State
const activeMode = ref('Ver Malla'); // 'Ver Malla' o 'Malla Simulada'
const selectedCareer = ref(careers[0].codigo);
const selectedPlan = ref(careers[0].plan);

const hoveredCourseId = ref(null);
const approvedCourseIds = ref(new Set());

// Derived state
const activeData = computed(() => {
  return careers.find(c => c.codigo === selectedCareer.value && c.plan === selectedPlan.value) || careers[0];
});

// Handlers
const handleModeChange = (mode) => {
  activeMode.value = mode;
  hoveredCourseId.value = null;
};

const handleHover = (courseId) => {
  if (activeMode.value === 'Ver Malla') {
    hoveredCourseId.value = courseId;
  }
};

const handleLeave = () => {
  if (activeMode.value === 'Ver Malla') {
    hoveredCourseId.value = null;
  }
};

const handleToggleApprove = (courseId) => {
  if (activeMode.value === 'Malla Simulada') {
    const newSet = new Set(approvedCourseIds.value);
    const course = activeData.value.asignaturas.find(c => c.id === courseId);
    
    if (!course) return;

    if (newSet.has(courseId)) {
      const removeCourseAndDependents = (idToRemove) => {
        newSet.delete(idToRemove);
        const cToRemove = activeData.value.asignaturas.find(c => c.id === idToRemove);
        if (cToRemove && cToRemove.abre_a) {
          cToRemove.abre_a.forEach(depId => {
            if (newSet.has(depId)) {
              removeCourseAndDependents(depId);
            }
          });
        }
      };
      removeCourseAndDependents(courseId);
    } else {
      // Only approve if it's available (all prerequisites are met)
      const canTake = course.prerrequisitos.length === 0 || course.prerrequisitos.every(reqId => newSet.has(reqId));
      if (canTake) {
        newSet.add(courseId);
      }
    }
    approvedCourseIds.value = newSet;
  }
};
</script>

<template>
  <div class="app-container">
    <TopBar 
      :careers="careers"
      :activeMode="activeMode"
      :selectedCareer="selectedCareer"
      :selectedPlan="selectedPlan"
      @update:mode="handleModeChange"
      @update:career="(val) => selectedCareer = val"
      @update:plan="(val) => selectedPlan = val"
    />
    
    <main class="main-content">
      <MallaGrid 
        :mallaData="activeData"
        :activeMode="activeMode"
        :hoveredCourseId="hoveredCourseId"
        :approvedCourseIds="approvedCourseIds"
        @hoverCourse="handleHover"
        @leaveCourse="handleLeave"
        @toggleApprove="handleToggleApprove"
      />
    </main>
    
    <div class="legend" v-if="activeMode === 'Ver Malla'">
      <span class="legend-item"><span class="color-box bg-purple"></span> Prerrequisito</span>
      <span class="legend-item"><span class="color-box bg-orange"></span> Seleccionado</span>
      <span class="legend-item"><span class="color-box bg-blue"></span> Abre a</span>
    </div>
    
    <div class="legend" v-if="activeMode === 'Malla Simulada'">
      <span class="legend-item"><span class="color-box bg-gray"></span> Aprobado</span>
      <span class="legend-item"><span class="color-box border-green"></span> Disponible</span>
      <span class="legend-item"><span class="color-box bg-light-gray"></span> Bloqueado</span>
    </div>
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  animation: fadeIn var(--transition-medium);
}

.main-content {
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.06);
  padding: 2rem;
  overflow-x: auto;
  min-height: 60vh;
}

.legend {
  display: flex;
  justify-content: center;
  gap: 2rem;
  padding: 1rem;
  margin-top: 1rem;
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.04);
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-size: 0.9rem;
  color: var(--text-primary);
  font-weight: 500;
}

.color-box {
  width: 16px;
  height: 16px;
  border-radius: 4px;
}
.bg-purple { background: var(--color-purple); }
.bg-orange { background: var(--color-orange); }
.bg-blue { background: var(--color-blue-light); }
.bg-gray { background: var(--color-gray-dark); }
.border-green { border: 2px solid var(--color-green); background: white; }
.bg-light-gray { background: var(--color-gray-light); border: 1px solid var(--color-gray-medium); }
</style>
