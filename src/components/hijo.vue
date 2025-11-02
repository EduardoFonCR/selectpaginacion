<template>
  <div class="hijo">
    <h3>Componente Hijo</h3>

    <!-- 📥 Recibe los datos y el valor del padre -->
    <selectpaginacion
      :pages="pages"
      v-model="localSelected"
      @selected="handleSelect"
    />

    <p>➡️ Selección hijo: {{ localSelected ? localSelected.nombre : 'Ninguna' }}</p>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import selectpaginacion from './selectpaginacion.vue'

// 🧩 Props que vienen del padre
const props = defineProps({
  pages: Array,
  selectedFromParent: Object
})

// 🔄 Evento que emite el hijo cuando selecciona algo
const emit = defineEmits(['selected'])

// 🗃 Valor interno del hijo (controlado con v-model)
const localSelected = ref(null)

// 🧭 Indicador de si el hijo está sincronizado con el padre
const syncedWithParent = ref(true)

// 👀 Cada vez que cambia el valor del padre, el hijo se sincroniza
watch(
  () => props.selectedFromParent,
  (newVal) => {
    localSelected.value = newVal
    syncedWithParent.value = true // vuelve a vincularse
  },
  { immediate: true } // corre al inicio
)

// 📤 Cuando el hijo selecciona algo, se “desvincula” hasta que el padre cambie
function handleSelect(option) {
  localSelected.value = option
  syncedWithParent.value = false
  emit('selected', option)
}
</script>

<style scoped>
.hijo {
  background: #fdfdfd;
  padding: 10px;
  border-radius: 8px;
  border: 1px solid #ccc;
  margin-top: 8px;
}
</style>
