<template>
  <div>
    <h2>Ejemplo con Padre e Hijos Sincronizados</h2>

    <!-- 🧭 SELECT DEL PADRE -->
    <selectpaginacion
      v-model="selectedPadre"
      :pages="paginas"
    />

    <p>🔹 Selección padre: {{ selectedPadre ? selectedPadre.nombre : 'Ninguna' }}</p>

    <div class="hijos">
      <!-- 🧒 HIJO 1 -->
      <hijo
        :pages="paginas"
        :selectedFromParent="selectedPadre"
        @selected="handleHijoSelected(1, $event)"
      />

      <!-- 🧒 HIJO 2 -->
      <hijo
        :pages="paginas"
        :selectedFromParent="selectedPadre"
        @selected="handleHijoSelected(2, $event)"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import selectpaginacion from './selectpaginacion.vue'
import hijo from './hijo.vue'

// 📘 Datos simulados de ejemplo (con paginación)
const paginas = [
  {
    page: 1,
    elementos: 3,
    data: [
      { id: 1, nombre: "Matemáticas", codigo: "MAT101" },
      { id: 2, nombre: "Español", codigo: "ESP202" },
      { id: 3, nombre: "Inglés", codigo: "ING303" }
    ]
  },
  {
    page: 2,
    elementos: 3,
    data: [
      { id: 4, nombre: "Ciencias", codigo: "CIE404" },
      { id: 5, nombre: "Historia", codigo: "HIS505" },
      { id: 6, nombre: "Música", codigo: "MUS606" }
    ]
  }
]

// 🔹 Estado reactivo del select del padre
const selectedPadre = ref(null)

// 🔸 Objeto para guardar lo que selecciona cada hijo
const hijosSeleccionados = ref({})

// 📤 Recibe los eventos emitidos por los hijos
function handleHijoSelected(num, seleccion) {
  hijosSeleccionados.value[num] = seleccion
  console.log(`Hijo ${num} seleccionó:`, seleccion)
}
</script>
