<template>
  <div class="select-container" ref="dropdown">
    <div class="select-box" @click="toggleDropdown">
      <span>
        {{ internalSelected ? internalSelected.codigo + ' - ' + internalSelected.nombre : 'Pág ' + currentPage }}
      </span>
      <button class="clear-btn" v-if="internalSelected" @click.stop="clearSelection">✖</button>
    </div>

    <div v-if="isOpen" class="dropdown-menu">
      <!-- PAGINACIÓN -->
      <div class="pagination-controls">
        <button @click.stop="prevPage" :disabled="currentPage === 1">⬅</button>
        <span>Pág {{ currentPage }}</span>
        <button @click.stop="nextPage" :disabled="currentPage === pages.length">➡</button>
      </div>

      <!-- OPCIONES -->
      <ul class="options">
        <li
          v-for="item in currentData?.data || []"
          :key="item.id"
          @click="selectOption(item)"
        >
          {{ item.codigo }} - {{ item.nombre }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted, onBeforeUnmount } from 'vue'

// Props
const props = defineProps({
  pages: {
    type: Array,
    required: true
  },
  modelValue: {
    type: Object,
    default: null
  }
})

const emit = defineEmits(['pageChanged', 'update:modelValue', 'selected'])

const isOpen = ref(false)
const currentPage = ref(1)
const internalSelected = ref(props.modelValue)

// 🔄 Datos de la página actual
const currentData = computed(() =>
  props.pages.find((p) => p.page === currentPage.value)
)

// 🧭 Mantiene sincronía entre el v-model externo e interno
watch(
  () => props.modelValue,
  (newVal) => {
    internalSelected.value = newVal
  }
)

// 🧩 Abrir/cerrar dropdown
function toggleDropdown() {
  isOpen.value = !isOpen.value
}

// ❌ Limpiar selección
function clearSelection() {
  internalSelected.value = null
  emit('update:modelValue', null)
}

// ✅ Seleccionar opción
function selectOption(option) {
  internalSelected.value = option
  emit('update:modelValue', option)
  emit('selected', option)
  isOpen.value = false
}

// ⬅➡ Paginación
function nextPage() {
  if (currentPage.value < props.pages.length) {
    currentPage.value++
    emit('pageChanged', currentData.value)
  }
}

function prevPage() {
  if (currentPage.value > 1) {
    currentPage.value--
    emit('pageChanged', currentData.value)
  }
}

// 🚪 Cerrar dropdown si se hace clic fuera
const dropdown = ref(null)
function handleClickOutside(event) {
  if (dropdown.value && !dropdown.value.contains(event.target)) {
    isOpen.value = false
  }
}

onMounted(() => document.addEventListener('click', handleClickOutside))
onBeforeUnmount(() => document.removeEventListener('click', handleClickOutside))
</script>

<style scoped>
.select-container {
  position: relative;
  width: 260px;
  font-family: 'Segoe UI', Tahoma, sans-serif;
  color: #111;
}

.select-box {
  border: 1px solid #444;
  background: #fff;
  color: #111;
  padding: 8px 12px;
  border-radius: 8px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
  transition: border 0.2s ease, box-shadow 0.2s ease;
}

.select-box:hover {
  border-color: #222;
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.2);
}

.clear-btn {
  background: transparent;
  border: none;
  color: #c00;
  cursor: pointer;
  font-size: 14px;
  margin-left: 8px;
  transition: color 0.2s;
}

.clear-btn:hover {
  color: #900;
}

.dropdown-menu {
  position: absolute;
  top: calc(100% + 4px);
  left: 0;
  width: 100%;
  background: #fff;
  color: #111;
  border: 1px solid #444;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.25);
  z-index: 100;
  overflow: hidden;
  animation: fadeIn 0.2s ease-in-out;
}

.pagination-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 6px 10px;
  background: #f0f0f0;
  border-bottom: 1px solid #ddd;
  font-weight: 500;
}

.pagination-controls button {
  border: none;
  background: #eaeaea;
  color: #111;
  cursor: pointer;
  border-radius: 4px;
  padding: 2px 6px;
  transition: background 0.2s;
}

.pagination-controls button:hover:not(:disabled) {
  background: #ddd;
}

.pagination-controls button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.options {
  list-style: none;
  padding: 0;
  margin: 0;
  max-height: 180px;
  overflow-y: auto;
}

.options li {
  padding: 8px 12px;
  cursor: pointer;
  transition: background 0.15s;
}

.options li:hover {
  background: #e9e9e9;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-5px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
