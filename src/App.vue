<script setup>
// onMounted se ejecuta después de que el componente se ha montado al DOM
// computed para datos reactivos se recomienda utilizar una propiedad calculada
// watch se utiliza para observar los cambios de una variable
import { ref, onMounted, computed, watch } from 'vue';

const tasks = ref([]);
const name = ref('');
const input_content = ref('');
const input_category = ref(null);

// Definir una propiedad computada para ordenar las tareas por fecha de creación
const tasks_asc = computed(() => {
  return tasks.value.slice().sort((a, b) => a.createdAt - b.createdAt);
});

const addTask = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return;
  }
  tasks.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  });
  input_content.value = '';
  input_category.value = null;
};

const removeTask = (task) => {
  //otra manera de eliminar un elemento de un arreglo
  tasks.value = tasks.value.filter(t => t !== task);
}

//guarda las tareas en el local storage
watch(tasks, newVal => {
   //le asigna al localstorage las tareas que se estan ingresando
  localStorage.setItem('tasks', JSON.stringify(newVal));
}, { deep: true }); //lo que hacemos con deep:true es que observe los cambios de las propiedades internas

//para que cuando se actualice no se borren midatos lo que hago es utilizar la propiedad watch
watch(name, (newVal) => {
   //mediante el localstorage le asigno el nombre que se esta ingresando
  localStorage.setItem('name', newVal);
});

//cuando se monte el componente se va a ejecutar esta funcion
onMounted(() => {
  //mediante el localstorage obtengo el nombre que se guardo
  name.value = localStorage.getItem('name') || '';
  tasks.value = JSON.parse(localStorage.getItem('tasks')) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Welcome to form <input type="text" placeholder="Name here" v-model="name"/>
      </h2>
    </section>
    
    <section class="create-task">
      <form @submit.prevent="addTask">
        <h4>What's on your task list?</h4>
        <input type="text" placeholder="make a" v-model="input_content"/>
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add task"/>
      </form>
    </section>

    <section class="task-list">
      <div class="list">
        <div v-for="task in tasks_asc" :key="task.createdAt" :class="`task-item ${task.done ? 'done' : ''}`">
          <label>
            <input type="checkbox" v-model="task.done"/>
            <span :class="`bubble ${task.category}`"></span>
          </label>
          <div class="task-content">
            <input type="text" v-model="task.content"/>
          </div>
          <div class="actions">
            <button class="delete" @click="removeTask(task)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>

/*Toma todos los elementos que no tengan el aributo radio ni checkbox */
input:not([type="radio"]):not([type="checkbox"]), button {
  appearance: none;
  border: none;
  outline: none;
  background: none;
  cursor: initial;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Arial', sans-serif;
}

body {
  background: #eee;
  color: #313154;
}

section {
  margin: 2rem;
  margin-bottom: 2rem;
  padding-left: 1.5rem;
  padding-right: 1.5rem;
}

h4 {
  color: #313154;
  font-size: 0.875rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
  margin-top: 0.5rem;
}

.greeting .title {
  display: flex;
  justify-content: center;
  align-items: center;
  color: #313154;
  font-weight: 800;
  font-size: 2rem;
}

.greeting .title input {
  margin-left: 1rem;
  flex: 1 1 0%;
  min-width: 0;
  color: #313154;
  font-size: 1.5rem;
  font-weight: 700;
  padding: 0.5rem;
  background: #fff;
  border-radius: 0.5rem;
}

.create-task input[type="text"] {
  display: block;
  width: 100%;
  font-size: 1.1rem;
  padding: 1rem;
  color: #313154;
  background-color: #fff;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  margin-bottom: 1rem;
}

.create-task .options {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
  margin-bottom: 1rem;
}

/*Estilos para las etiquetas de los formularios */
.create-task .options label {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 1.5rem;
  background-color: #fff;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  flex: 1;
}

input[type="radio"],
input[type="checkbox"] {
  display: none;
}

.bubble {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid #3A82EE;
  box-shadow: 0px 0px 4px rgba(58, 130, 238, 0.75);
}

.bubble.personal {
  border-color: #EA40A4;
  box-shadow: 0px 0px 4px rgba(234, 64, 164, 0.75);
}

.bubble::after {
  content: "";
  display: block;
  opacity: 0;
  width: 0px;
  height: 0px;
  background-color: #3A82EE;
  box-shadow: 0px 0px 4px rgba(58, 130, 238, 0.75);
  border-radius: 50%;
  transition: 0.2s ease-in-out;
}

.bubble.personal::after {
  background-color: #EA40A4;
  box-shadow: 0px 0px 4px rgba(234, 64, 164, 0.75);
}

input:checked ~ .bubble::after {
  width: 10px;
  height: 10px;
  opacity: 1;
}

.create-task .options label div {
  color: #313154;
  font-size: 1.125rem;
  margin-top: 1rem;
}

.create-task input[type="submit"] {
  display: block;
  width: 100%;
  font-size: 1.125rem;
  padding: 1rem 1.5rem;
  color: #FFF;
  background-color: #EA40A4;
  border-radius: 0.5rem;
  box-shadow: 0px 0px 4px rgba(234, 64, 164, 0.75);
  cursor: pointer;
  transition: 0.2s ease-in-out;
}

.create-task input[type="submit"]:hover {
  background-color: #D9318C;
}

.task-list .list {
  margin: 1rem 0;
}

.task-list .task-item {
  display: flex;
  align-items: center;
  background-color: #fff;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  margin-bottom: 1rem;
}

.task-item label {
  display: block;
  margin-right: 1rem;
  cursor: pointer;
}

.task-item .task-content {
  flex: 1 1 0%;
}

.task-item .task-content input {
  width: 100%;
  color: #313154;
  font-size: 1.1rem;
  padding: 0.5rem;
}

.task-item .actions {
  display: flex;
  align-items: center;
}

.task-item .actions button {
  display: block;
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  color: #FFF;
  cursor: pointer;
  transition: 0.2s ease-in-out;
}

.task-item .actions button:hover {
  opacity: 0.75;
}

.task-item .actions .delete {
  background-color: #ff5b57;
}

.task-item.done .task-content input {
  text-decoration: line-through;
  color: #888;
}
</style>
