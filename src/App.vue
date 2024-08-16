<!-- src/App.vue -->
<template>
  <div class="container mx-auto p-4 max-w-md bg-gray-100 rounded-lg shadow">
    <h1 class="text-3xl font-bold mb-4 text-center text-gray-800">Todo App</h1>
    <div class="mb-4 flex">
      <input
        v-model="newTodo"
        @keyup.enter="addTodo"
        type="text"
        placeholder="Add a new todo"
        class="w-full p-2 border rounded-l focus:outline-none focus:ring-2 focus:ring-blue-300"
      />
      <button
        @click="addTodo"
        class="bg-blue-500 text-white px-4 py-2 rounded-r hover:bg-blue-600 transition duration-200"
      >
        Add
      </button>
    </div>
    <div v-if="todos.length > 0" class="mb-4 flex items-center">
      <input
        type="checkbox"
        v-model="selectAll"
        @change="toggleSelectAll"
        class="mr-2 form-checkbox h-5 w-5 text-blue-600"
      />
      <span class="mr-2">Select All</span>
      <button
        @click="deleteSelected"
        class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600 transition duration-200"
      >
        Delete Selected
      </button>
    </div>
    <ul class="space-y-2">
      <li
        v-for="(todo, index) in todos"
        :key="index"
        class="flex items-center bg-white p-2 rounded shadow"
      >
        <input
          type="checkbox"
          v-model="todo.selected"
          class="mr-2 form-checkbox h-5 w-5 text-blue-600"
        />
        <input
          v-if="todo.editing"
          v-model="todo.text"
          @blur="finishEdit(todo)"
          @keyup.enter="finishEdit(todo)"
          type="text"
          class="flex-grow p-1 border rounded mr-2 focus:outline-none focus:ring-2 focus:ring-blue-300"
          v-focus
        />
        <span
          v-else
          :class="{
            'flex-grow': true,
            'line-through text-gray-500': todo.completed,
            'text-gray-800': !todo.completed
          }"
          @dblclick="startEdit(todo)"
        >
          {{ todo.text }}
        </span>
        <button
          v-if="!todo.editing"
          @click="startEdit(todo)"
          class="bg-yellow-500 text-white px-2 py-1 rounded mr-2 hover:bg-yellow-600 transition duration-200"
        >
          Edit
        </button>
        <button
          @click="removeTodo(index)"
          class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600 transition duration-200"
        >
          Delete
        </button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const newTodo = ref('')
const todos = ref([])
const selectAll = ref(false)

const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({ text: newTodo.value, completed: false, editing: false, selected: false })
    newTodo.value = ''
  }
}

const removeTodo = (index) => {
  todos.value.splice(index, 1)
}

const startEdit = (todo) => {
  todo.editing = true
}

const finishEdit = (todo) => {
  todo.editing = false
  todo.text = todo.text.trim()
  if (!todo.text) {
    removeTodo(todos.value.indexOf(todo))
  }
}

const toggleSelectAll = () => {
  todos.value.forEach(todo => todo.selected = selectAll.value)
}

const deleteSelected = () => {
  todos.value = todos.value.filter(todo => !todo.selected)
  selectAll.value = false
}

// Custom directive for autofocus
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<script>
export default {
  directives: {
    focus: {
      mounted: (el) => el.focus()
    }
  }
}
</script>