<script setup>
import { ref, onMounted, computed, watch } from 'vue'

// Define a reactive reference to store the list of todos
const todos = ref([])

// Define a reactive reference to store a name
const name = ref('')

// Define reactive references for input content and category
const input_content = ref('')
const input_category = ref(null)

// Define a computed property that sorts todos in ascending order by createdAt timestamp
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

// Watch for changes in the 'name' variable and store it in localStorage of the browser
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// Watch for changes in the 'todos' array and store it in localStorage (deep watch)
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true
})

// Define a function to add a new todo to the 'todos' array
const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })
}

// Define a function to remove a todo from the 'todos' array
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

// Perform actions when the component is mounted
onMounted(() => {
  // Retrieve the 'name' and 'todos' data from localStorage
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <!-- Input field for 'name' with two-way binding -->
      <h2 class="title">What's up, <input type="text" id="name" placeholder="Enter You Name" v-model="name"></h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <!-- Input field for 'input_content' with two-way binding -->
        <input type="text" name="content" id="content" placeholder="e.g. learn python" v-model="input_content">

        <h4>Pick a category</h4>
        <div class="options">
          <!-- Radio buttons for selecting 'input_category' -->
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <!-- Loop through 'todos_asc' and display each todo -->
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <!-- Checkbox for marking a todo as done -->
            <input type="checkbox" v-model="todo.done" />
            <!-- Display a colored bubble based on the category -->
            <span :class="`bubble ${
              todo.category == 'business'
                ? 'business'
                : 'personal'
            }`"></span>
          </label>
          <div class="todo-content">
            <!-- Input field for editing todo content with two-way binding -->
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <!-- Button to delete a todo -->
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
