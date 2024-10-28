<template>
  <aside class="bg-base-200 w-72 min-h-screen">
    <h2 class="text-lg font-bold mx-4">Proyectos</h2>
    <p v-if="proyectosStore.proyectos.length === 0" class="text-sm text-gray-500 mx-4">No hay proyectos</p>

    <!-- Menu -->
    <ul class="menu">
      <li v-for="(proyecto, index) in proyectosStore.proyectos" :key="index">
        <router-link 
          :to="{ name: 'proyectoID', params: { id: proyecto.id } }" 
          class="flex items-center p-4 hover:bg-pink-300 transition-colors"
        >
          {{ index + 1 }} {{ proyecto.nombre }}
        </router-link>
      </li>
    </ul>
  </aside>
</template>

<script lang="ts" setup>
import { useProyectosStore } from '../store/projects.store';
const proyectosStore = useProyectosStore();
</script>
