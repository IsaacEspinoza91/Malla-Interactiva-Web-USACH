<script setup>
import { computed } from 'vue';
import CourseCard from './CourseCard.vue';

const props = defineProps({
  mallaData: Object,
  activeMode: String,
  hoveredCourseId: Number,
  approvedCourseIds: Object
});

const emit = defineEmits(['hoverCourse', 'leaveCourse', 'toggleApprove']);

const semesters = computed(() => {
  const total = props.mallaData.configuracion.total_semestres;
  const sems = Array.from({ length: total }, (_, i) => ({
    id: i + 1,
    courses: []
  }));
  
  props.mallaData.asignaturas.forEach(course => {
    if (course.semestre > 0 && course.semestre <= total) {
      sems[course.semestre - 1].courses.push(course);
    }
  });
  
  return sems;
});

// Helper derived data since we need it in the template
const statuses = computed(() => {
  const map = {};
  const courses = props.mallaData.asignaturas;
  
  if (props.activeMode === 'Ver Malla') {
    if (props.hoveredCourseId !== null) {
      courses.forEach(c => { map[c.id] = 'dimmed'; });

      map[props.hoveredCourseId] = 'hovered';
      const hoveredCourse = courses.find(c => c.id === props.hoveredCourseId);
      if (hoveredCourse) {
        hoveredCourse.prerrequisitos.forEach(id => {
          map[id] = 'prerequisite';
        });
        hoveredCourse.abre_a.forEach(id => {
          map[id] = 'opened';
        });
      }
    } else {
      courses.forEach(c => { map[c.id] = 'default'; });
    }
  } else if (props.activeMode === 'Malla Simulada') {
    courses.forEach(course => {
      if (props.approvedCourseIds.has(course.id)) {
        map[course.id] = 'approved';
      } else {
        // check if prerequisites are met
        const canTake = course.prerrequisitos.length === 0 || course.prerrequisitos.every(reqId => props.approvedCourseIds.has(reqId));
        if (canTake) {
          map[course.id] = 'available';
        } else {
          map[course.id] = 'locked';
        }
      }
    });
  }
  return map;
});
</script>

<template>
  <div class="malla-grid-wrapper">
    <div class="malla-grid" :style="{ gridTemplateColumns: `repeat(${mallaData.configuracion.total_semestres}, minmax(130px, 1fr))` }">
      <div class="semester-col" v-for="sem in semesters" :key="sem.id">
        <div class="semester-header">
          Semestre {{ sem.id }}
        </div>
        <div class="courses-container">
          <CourseCard 
            v-for="course in sem.courses" 
            :key="course.id"
            :course="course"
            :status="statuses[course.id]"
            :activeMode="activeMode"
            @mouseenter="emit('hoverCourse', course.id)"
            @mouseleave="emit('leaveCourse')"
            @click="emit('toggleApprove', course.id)"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.malla-grid-wrapper {
  overflow-x: auto;
  padding-bottom: 1rem;
}

.malla-grid {
  display: grid;
  gap: 1.25rem;
  width: 100%;
  min-width: 1000px; /* Base min-width for grid */
}

.semester-col {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.semester-header {
  text-align: center;
  font-weight: 600;
  color: var(--text-secondary);
  padding-bottom: 0.5rem;
  border-bottom: 2px solid var(--color-gray-medium);
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.courses-container {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  flex: 1;
}
</style>
