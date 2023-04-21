<template>
    <div>
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </label>
            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>
            <!-- botones de accion -->
            <div class="actions">
              <!-- activara la funcion borrar y se le pasara la tarea que vamos a eliminar -->
              <button class="delete" @click="$emit('removeTodo')">Borrar</button>
              <!-- <button class="delete" @click="removeTodo(todo)">Borrar</button> -->
              
            </div>
          </div>
</template>

<script setup>
const props = defineProps({
  todo: {
    type: Object,
  },
});

</script>

<style>

</style>