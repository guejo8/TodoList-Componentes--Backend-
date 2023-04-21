<script setup>
import ListTask from "./ListTask.vue";
//
//created = se ejecuta antes del onMounted pero no se tiene acceso al DOM
//onMounted = Cuando el componente se ha montado o hay cosas visibles (tengo acceso al DOM)...

//computed = propiedades computadas quedan almacenadas en la cache del pc,y son propiedades que calculan
// y devuelven un valor basado en uno o más valores reactivos. Estas propiedades se actualizan automáticamente cuando sus dependencias reactivas cambian
// Básicamente una computada es una variable, la diferencia con las Variables de Vue es que las computadas normalmente transforman la variable o hacen algún tipo de cálculo antes de devolverla.
// Es por eso que para cualquier lógica compleja, deberia usar una propiedad computada.

// watch = es una forma de reaccionar a cambios en una o varias variables reactivas. Al utilizar esta propiedad, podemos ejecutar una función cada vez que una variable reactiva observada cambia.
// Creamos una función que se ejecutará cada vez que la variable "valor" cambie:
//  observador personalizado =     watch(valor, (nuevoValor, antiguoValor) => {

import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);
//ascendente (lista por fecha. mostrar el ultimo por delantado (.createdAt))
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    // ordenamos el array   y     teoria  .createdAt
    // 0 <    -> a esta en un indice menor que b (resultado #negativo)
    // 0      a y b podrian estar iguales
    // >0     -> a esta en un indice mayor que b (resultado positivo)
    //De forma predeterminada, la sort()función ordena los valores como cadenas .
    //Esto funciona bien para cadenas ("Apple" viene antes de "Banana").
    //Sin embargo, si los números se ordenan como cadenas, "25" es mayor que "100", porque "2" es mayor que "1".
    //Debido a esto, el sort()método producirá un resultado incorrecto al ordenar números.
    //Puede solucionar esto proporcionando una función de comparación :

    //.createdAt
    //En el ejemplo proporcionado, la propiedad .createdAt es una propiedad de cada elemento en el array todos, y representa la fecha en que se creó el elemento.
    //Es importante destacar que la propiedad .createdAt no es una propiedad predefinida en JavaScript, sino que es una propiedad personalizada definida por el programador. En otras palabras, es posible que la propiedad .createdAt no exista en todos los elementos del array todos, o que tenga un valor nulo o indefinido.
    //La función de la propiedad .createdAt en este ejemplo es proporcionar una fecha de referencia para ordenar los elementos en el array todos de forma ascendente. Al comparar los valores de .createdAt de cada elemento, se puede determinar el orden en que se crearon los elementos.
    //Es importante tener en cuenta que la propiedad .createdAt no es una función en sí misma, sino simplemente una propiedad que contiene un valor de fecha. En el código de Vue 3 proporcionado, se accede a esta propiedad usando la notación de punto, como a.createdAt y b.createdAt. Esto permite que la función de comparación en sort() acceda al valor de la propiedad .createdAt de cada elemento del array todos
    return b.createdAt - a.createdAt;
  })
);
//Añadir tareas con sus valores
const addTodo = () => {
  //Verificamos si solo se han agregado espacion en el input o no se ha seleccionado una categoria
  if (input_content.value.trim() === "" || input_category.value === null) {
    //si uno esta vacio o el otro bull, no se hara nada
    return;
  }
  console.log("Añadir la tarea...");
  //insertar contenido
  todos.value.push({
    //se ingresara algo de contenido que vendra de (input_content.value)
    content: input_content.value,
    //nuestro valor de categoria
    category: input_category.value,
    //de manera predeterminada no se establecera nada como hecho
    done: false,
    //crear la nueva fecha en milisegundos (.getTime()) que usaremos para comparar
    createdAt: new Date().getTime(),
  });
  //resetear valores de los campos depues que se añaden
  input_content.value = "";
  input_category.value = null;
};

//Funcion para eliminar tarea
const removeTodo = (todo) => {
  // (t = cada elemento) vamos a recorrer cada una de las tareas y verificamos si no es igual a
  // todo, luego se agregara nuevamente  la matriz.
  //El filtro utiliza una función de flecha que compara cada elemento "t" en el array con el parámetro "todo" utilizando el operador !== (distinto de) y devuelve un nuevo array con todos los elementos que no son iguales a "todo".
  // si es igual, no se agregara a la matriz y devolvera falso
  todos.value = todos.value.filter((t) => t !== todo);
};

//Agregar las tareas ya añadidas al LS
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);
//deep = revisara todo, cada elemento de la matriz individual aqui(todos.value.push) y si
//alguno de ellos cambia, lo detectara y luego llamaremos a la funcion
//( localStorage.setItem("todos", JSON.stringify(newVal)) otra vez

//miramos el "name"  y si este cambia,obtendremos el nuevo valor y
//vamos a configurarlo en nuestro almacenamiento.
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});
//obtenemos de LS
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  //convertimos
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
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
