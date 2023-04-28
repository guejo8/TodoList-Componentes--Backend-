<script setup>
import ListTask from "./ListTask.vue";
import { ref, onMounted, computed, watch } from "vue";
import axios from 'axios';

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);
const input_editing = ref(false);
const todo_editing = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = async () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  console.log("Añadir la tarea...");
  const newTodo = {
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  };
  try {
    const response = await axios.post("http://localhost:3000/tareasBack", newTodo);
    todos.value.push(response.data);
    input_content.value = "";
    input_category.value = null;
  } catch (error) {
    console.error(error);
  }
};

const removeTodo = async (todo) => {
  try {
    await axios.delete(`http://localhost:3000/tareasBack/${todo.id}`);
    todos.value = todos.value.filter((t) => t !== todo);
  } catch (error) {
    console.error(error);
  }
};

const editTodo = (todo) => {
  input_editing.value = true;
  todo_editing.value = todo;
  input_content.value = todo.content;
  input_category.value = todo.category;
};

const updateTodo = async () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  console.log("Modificando tarea...");
  const updatedTodo = {
    ...todo_editing.value,
    content: input_content.value,
    category: input_category.value,
  };
  try {
    await axios.put(`http://localhost:3000/tareasBack/${updatedTodo.id}`, updatedTodo);
    const index = todos.value.findIndex((t) => t.id === updatedTodo.id);
    todos.value.splice(index, 1, updatedTodo);
    input_content.value = "";
    input_category.value = null;
    input_editing.value = false;
    todo_editing.value = null;
  } catch (error) {
    console.error(error);
  }
};

const cancelEditing = () => {
  input_editing.value = false;
  todo_editing.value = null;
  input_content.value = "";
  input_category.value = null;
};

const captarTodos = async () => {
  try {
    const response = await axios.get("http://localhost:3000/tareasBack");
    todos.value = response.data;
  } catch (error) {
    console.error(error);
  }
};

watch(todos, async (newVal) => {
  try {
    await axios.put(`http://localhost:3000/tareasBack`, newVal);
  } catch (error) {
    console.error(error);
  }
}, { deep: true });

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(async () => {
  name.value = localStorage.getItem("name") || "";
  await captarTodos();
});

</script>

<template>
  <div class="contenedor">
    <main class="app">
      <section class="greeting">
        <h2 class="title">
          Hola!!,
          <input
            class="inputNombre"
            type="text"
            placeholder="Tu Nombre..."
            v-model="name"
          />
        </h2>
        <div class="cajaImagen">
          <img class="icono floating" src="../assets/img/astronauta.png" />
        </div>
      </section>
      <section class="create-todo">
        <h3>CREA TU LISTA</h3>
        <form @submit.prevent="addTodo">
          <h4>¿Que vamos a hacer?</h4>
          <input
            type="text"
            class="inputTarea"
            placeholder="Ingresa tu tarea..."
            v-model="input_content"
          />
          <h4>Selecciona una categoría</h4>
          <div class="options">
            <label>
              <input
                type="radio"
                name="category"
                value="business"
                v-model="input_category"
              />
              <span class="bubble business"></span>
              <div>Trabajo</div>
            </label>
            <label>
              <input
                type="radio"
                name="category"
                value="personal"
                v-model="input_category"
              />
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>
          <input type="submit" value="Añadir" />
        </form>
      </section>
      <!-- Enumerar tareas pendientes -->
      <sectiom class="todo-list">
        <h3>Tu Lista</h3>
        <div class="list">
          <!-- se mostrara cada lista de tareas QUE ESTA EN OTRO COMPONENTE-->
          <!-- haremos un recorrido y con plantillas literal comprobamos si esta hecha o no la tarea -->
          <!-- Esta plantilla de cadena de texto genera una cadena que consiste en la clase CSS todo-item y la clase CSS condicional done. 
          La clase done solo se agrega si la propiedad done del objeto todo es verdadera, lo que se logra utilizando el operador && en una expresión booleana. -->
          <ListTask :todo="todo" v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`"  @removeTodo="removeTodo(todo)"/>
        
        </div>
        
      </sectiom>
    </main>
  </div>
</template>

<style scoped>
.footerComponente {
  margin-top: 2rem;
}
</style>
