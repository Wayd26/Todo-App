<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([]);
const name = ref("")

const inputContent = ref("")
const inputCategory = ref(null)

const todosAsc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

const addTodo = () => {
    if(inputContent.value.trim() === '' || inputCategory.value === null) {
      return
    } 
    todos.value.push({
      content: inputContent.value,
      category: inputCategory.value,
      done: false,
      createdAt: new Date().getTime()
    })
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}


watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
})

watch(todos, (newVal) => {
  localStorage.setItem("todos", JSON.stringify(newVal))
}, { deep: true })

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
})

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Aloha 👋, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="e. g. learn Vue" v-model="inputContent" />

        <h4>Pick a category please</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="inputCategory" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="inputCategory" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />

      </form>
    </section>

    <setcion class="todo-list">
        <h3 v-if="todosAsc.length !== 0">TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todosAsc" :class="`todo-item ${todo.done && 'done'}`">
        <label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div></div>
      </div>
    </setcion>
  </main>
</template>

