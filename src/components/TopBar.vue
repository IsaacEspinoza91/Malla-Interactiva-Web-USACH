<script setup>
import { computed } from 'vue';

const props = defineProps({
  careers: Array,
  activeMode: String,
  selectedCareer: Number,
  selectedPlan: String,
});

const emit = defineEmits(['update:mode', 'update:career', 'update:plan']);

const availablePlans = computed(() => {
  const matching = props.careers.filter(c => c.codigo === props.selectedCareer).map(c => c.plan);
  return [...new Set(matching)];
});

const uniqueCareers = computed(() => {
  const seen = new Set();
  const arr = [];
  for (const c of props.careers) {
    if (!seen.has(c.codigo)) {
      seen.add(c.codigo);
      arr.push({ codigo: c.codigo, carrera: c.carrera });
    }
  }
  return arr;
});
</script>

<template>
  <header class="topbar">
    <div class="logo">
      <h1>Malla Interactiva USACH</h1>
    </div>
    
    <div class="controls">
      <div class="select-group">
        <label>Carrera</label>
        <select :value="selectedCareer" @change="e => emit('update:career', parseInt(e.target.value))">
          <option v-for="c in uniqueCareers" :key="c.codigo" :value="c.codigo">
            {{ c.carrera }}
          </option>
        </select>
      </div>
      
      <div class="select-group">
        <label>Plan</label>
        <select :value="selectedPlan" @change="e => emit('update:plan', e.target.value)">
          <option v-for="p in availablePlans" :key="p" :value="p">
            {{ p }}
          </option>
        </select>
      </div>

      <div class="mode-toggles">
        <button 
          :class="{ active: activeMode === 'Ver Malla' }"
          @click="emit('update:mode', 'Ver Malla')"
        >
          Ver Malla
        </button>
        <button 
          :class="{ active: activeMode === 'Malla Simulada' }"
          @click="emit('update:mode', 'Malla Simulada')"
        >
          Malla Simulada
        </button>
      </div>
    </div>
  </header>
</template>

<style scoped>
.topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 1.5rem;
  padding-bottom: 1.5rem;
}

.logo h1 {
  color: var(--color-green);
  font-size: 1.7rem;
  font-weight: 700;
  letter-spacing: -0.5px;
}

.controls {
  display: flex;
  align-items: flex-end;
  gap: 1.5rem;
  flex-wrap: wrap;
}

.select-group {
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}

.select-group label {
  font-size: 0.75rem;
  color: var(--text-secondary);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.select-group select {
  min-width: 180px;
  background-color: var(--color-gray-light);
  border: 1px solid transparent;
}
.select-group select:hover {
  border: 1px solid var(--color-gray-medium);
}
.select-group select:focus {
  background-color: white;
}

.mode-toggles {
  display: flex;
  gap: 0.5rem;
  background: var(--color-gray-light);
  padding: 0.4rem;
  border-radius: 8px;
  height: 100%;
}

.mode-toggles button {
  border: none;
  background: transparent;
  color: var(--text-secondary);
  font-weight: 600;
  padding: 0.5rem 1rem;
}

.mode-toggles button:hover {
  color: var(--color-green);
}

.mode-toggles button.active {
  background: white;
  color: var(--color-orange);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}
</style>
