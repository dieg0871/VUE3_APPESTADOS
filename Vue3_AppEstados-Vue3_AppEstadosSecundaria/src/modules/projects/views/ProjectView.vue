<template>
  <div class="p-8 overflow-x-auto w-full">
    <h2>Nombre Proyecto: {{ proyectoActual?.nombre }}</h2>

    <div class="mt-4 flex items-center">
      <input 
        v-model="nuevaTarea" 
        type="text" 
        placeholder="Agregar nueva tarea" 
        class="input input-borderd mr-2" 
      />
      <button @click="agregarTarea" class="btn btn-secondary">Agregar</button>

      <!-- Barra de progreso de tareas completadas -->
      <div class="flex items-center">
        <span class="mr-2">Tareas Completadas:</span>
        <progress :value="progresoCompletado" max="100" class="progress progress-secondary w-56"></progress>
        <span class="ml-2">{{ progresoCompletado }}%</span>
      </div>
    </div>

    <table class="table mt-4">
      <thead>
        <tr>
          <th>#</th>
          <th>Completada</th>
          <th>Tarea</th>
          <th>Fecha y hora de Registro</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(tarea, index) in proyectoActual?.tareas" :key="tarea.id">
          <td>{{ index + 1 }}</td>
          <td>
            <input
              type="checkbox"
              v-model="tarea.completada"
              @change="actualizarProgresoCompletado"
            />
          </td>
          <td>{{ tarea.nombre }}</td>
          <td>{{ tarea.fecha }}</td>
          <td>
            <button @click="prepararActualizacion(tarea)" class="btn btn-warning btn-sm">Actualizar</button>
            <button @click="eliminarTarea(tarea.id)" class="btn btn-danger btn-sm">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>

    <div v-if="tareaParaActualizar" class="mt-4">
      <h3>Actualizar Tarea</h3>
      <input 
        v-model="tareaParaActualizar.nombre" 
        type="text" 
        placeholder="Nombre de la tarea" 
        class="input input-borderd mr-2" 
      />
      <button @click="actualizarTarea" class="btn btn-success">Guardar Cambios</button>
      <button @click="cancelarActualizacion" class="btn btn-primary">Cancelar</button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue';
import { useRoute } from 'vue-router';
import { useProyectosStore } from '../store/projects.store';

const proyectosStore = useProyectosStore();
const route = useRoute();
const nuevaTarea = ref('');
const tareaParaActualizar = ref(null);

const proyectoActual = computed(() =>
  proyectosStore.proyectos.find((proyecto) => proyecto.id === route.params.id)
);

// Progreso de tareas completadas
const progresoCompletado = computed(() => {
  const totalTareas = proyectoActual.value?.tareas.length || 0;
  const tareasCompletadas = proyectoActual.value?.tareas.filter(t => t.completada).length || 0;
  return totalTareas ? Math.round((tareasCompletadas / totalTareas) * 100) : 0;
});

const agregarTarea = () => {
  const proyecto = proyectoActual.value;
  if (nuevaTarea.value.trim() !== '') {
    if (proyecto?.tareas.length < 10) {
      const fechaActual = new Date().toLocaleString();
      const nuevaTareaObj = {
        id: Date.now().toString(),
        nombre: nuevaTarea.value.trim(),
        completada: false,
        fecha: fechaActual,
      };
      proyectosStore.agregarTarea(route.params.id as string, nuevaTareaObj);
      nuevaTarea.value = '';
    } else {
      alert('No se pueden agregar más de 10 tareas a este proyecto.');
    }
  }
};

// Actualiza solo el progreso de tareas completadas, sin afectar el estado del proyecto
const actualizarProgresoCompletado = () => {
  // Este método solo recalcula el progreso completado de tareas sin modificar el progreso general del proyecto
};

const prepararActualizacion = (tarea) => {
  tareaParaActualizar.value = { ...tarea };
};

const actualizarTarea = () => {
  if (tareaParaActualizar.value) {
    const proyecto = proyectoActual.value;
    const index = proyecto.tareas.findIndex(t => t.id === tareaParaActualizar.value.id);
    if (index !== -1) {
      proyecto.tareas[index].nombre = tareaParaActualizar.value.nombre;
      proyecto.tareas[index].fecha = new Date().toLocaleString();
      tareaParaActualizar.value = null;
    }
  }
};

const cancelarActualizacion = () => {
  tareaParaActualizar.value = null;
};

const eliminarTarea = (tareaId) => {
  const proyectoId = route.params.id as string;
  proyectosStore.eliminarTarea(proyectoId, tareaId);
};
</script>
