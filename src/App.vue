<template>
  <div class="title">To-Do List</div>
  <div class="form">
    <input v-model="newTodo" type="text" class="form" :placeholder="displayedPlaceholder" @keyup.enter="addTask">
    <button class="add-button" @click="addTask">Add Task</button>
  </div>

  <div class="tasks">
    <transition-group name="slide-fade" tag="ul">
      <transition name="slide-right-fade">
        <span v-if="todos.length === 0 && dones.length === 0" class="nothing">Nothing for the moment :/</span>
      </transition>
      <li v-for="todo in todos" :key="todo.id" :class="{ done: false }">
        <CustomCheckbox :id="'todo-' + todo.id" v-model="todo.done" @change="completeTask(todo)">
          {{ todo.text }}
        </CustomCheckbox>
      </li>
      <li v-for="done in filteredDones" :key="done.id" :class="{ done: true }">
        <CustomCheckbox :id="'done-' + done.id" v-model="done.done" @change="restoreTask(done)">
          {{ done.text }}
        </CustomCheckbox>
      </li>
    </transition-group>
    <button @click.prevent="toggleDones" class="toggle-button">{{ show ? 'Hide' : 'Show' }} Completed Tasks</button>
    <p>Tasks Remaining: {{ taskCount }}</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import CustomCheckbox from '@/components/CustomCheckbox.vue';

const todos = ref([]);

const dones = ref([]);

const newTodo = ref('');
const show = ref(true);

const taskCount = computed(() => {
  return todos.value.length;
});

const placeholderText = "Write what you have to do";
const displayedPlaceholder = ref("");
let currentIndex = 0;

function addTask() {
  if (newTodo.value.trim() !== '') {
    todos.value.unshift({
      id: todos.value.length + dones.value.length + 1,
      text: newTodo.value,
      done: false
    });
    newTodo.value = '';

    const button = document.querySelector('.add-button');
    button.classList.add('active-click');
    setTimeout(() => {
      button.classList.remove('active-click');
    }, 300); 
  }
}

function toggleDones() {
  show.value = !show.value;
}

const filteredDones = computed(() => {
  return show.value ? dones.value : [];
});

function completeTask(task) {
  todos.value = todos.value.filter((t) => t !== task);
  dones.value.push(task);
}

function restoreTask(done) {
  dones.value = dones.value.filter((d) => d !== done);
  todos.value.push(done);
}

let cursorVisible = true;

function typePlaceholder() {
  if (currentIndex < placeholderText.length) {
    displayedPlaceholder.value = placeholderText.slice(0, currentIndex + 1) + (cursorVisible ? '|' : '');
    currentIndex++;
    setTimeout(typePlaceholder, 100);
  } else {
    setTimeout(blinkCursor, 500);
  }
}

function blinkCursor() {
  cursorVisible = !cursorVisible;
  displayedPlaceholder.value = placeholderText + (cursorVisible ? '|' : '');
  setTimeout(blinkCursor, 500);
}

onMounted(() => {
  typePlaceholder();
});
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Grey+Qo&family=Manrope:wght@200&family=Playfair+Display:ital,wght@0,400..900;1,400..900&family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');

html {
  background-image: url('@/assets/layered-waves-haikei.png');
  background-size: cover;
  height: 100vh;
  font-family: 'Playfair Display';
}

.title {
  margin-left: 30px;
  font-size: 100px;
  display: flex;
  color: white;
}

.form {
  margin-left: 20px;
  width: 90%;
  height: 50px;
  display: flex;
}

.form input {
  border-radius: 8px 0 0 8px;
  border-color: green;
  height: 100%;
  flex-grow: 1;
  box-sizing: border-box;
  padding-left: 1em;
}

.form input::placeholder {
  font-size: 16px;
  color: #cccccc;
}

.form input {
  font-family: monospace;
}

.add-button {
  background-color: #04AA6D;
  border: none;
  color: white;
  padding: 0px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
  border-radius: 0px 10px 10px 0px;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.add-button:hover {
  transform: scale(1.05); 
}

.add-button:active {
  transform: scale(1.1); 
  box-shadow: 0 0 15px rgba(0, 255, 0, 0.7); 
}
.add-button.active-click {
  transform: scale(1.1); 
  box-shadow: 0 0 15px rgba(0, 255, 0, 0.7); 
}

.toggle-button {
  margin-top: 20px;
  background-color: #04AA6D;
  border: none;
  color: white;
  padding: 5px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
  border-radius: 5px 5px 5px 5px;
}

.tasks {
  padding-top: 20px;
  padding-left: 60px;
  color: white;
}

.tasks ul {
  margin: 0;
  padding: 0;
  list-style: none;
  font-size: 40px;
}

.tasks li {
  font-size: 22px;
  padding-bottom: 5px;
}

.tasks .done {
  text-decoration: line-through;
  color: #D3D3D3;
}

.slide-fade-enter-active {
  transition: opacity 0.3s ease, transform 0.3s ease, max-height 0.3s ease, margin 0.3s ease, padding 0.3s ease;
}

.slide-fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease, max-height 0.3s ease, margin 0.3s ease, padding 0.3s ease;
}

.slide-fade-enter-from {
  opacity: 0;
  transform: translateX(-20px);
  max-height: 0;
  margin-top: 0;
  margin-bottom: 0;
  padding-top: 0;
  padding-bottom: 0;
}

.slide-fade-enter-to {
  opacity: 1;
  transform: translateX(0);
  max-height: 50px;
  margin-top: 5px;
  margin-bottom: 5px;
  padding-top: 5px;
  padding-bottom: 5px;
  transition-delay: 0.5s;
}

.slide-fade-leave-from {
  opacity: 1;
  transform: translateX(0);
  max-height: 50px;
  margin-top: 5px;
  margin-bottom: 5px;
  padding-top: 5px;
  padding-bottom: 5px;
}

.slide-fade-leave-to {
  opacity: 0;
  transform: translateX(20px);
  max-height: 0;
  margin-top: 0;
  margin-bottom: 0;
  padding-top: 0;
  padding-bottom: 0;
  transition-duration: 0.5s;
}

.slide-right-fade-enter-active, .slide-right-fade-leave-active {
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.slide-right-fade-enter-from, .slide-right-fade-leave-to {
  opacity: 0;
  transform: translateX(20px); 
}

.slide-right-fade-enter-to, .slide-right-fade-leave-from {
  opacity: 1;
  transform: translateX(0); 
}


p {
  font-size: 25px;
}
</style>
