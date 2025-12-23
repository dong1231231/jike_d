<template>
  <div class="min-h-screen bg-gradient-to-br from-indigo-50 to-gray-50">
    <div class="max-w-4xl mx-auto px-4 py-6 pb-10">
      <header class="text-center mb-6">
        <h1 class="text-3xl font-bold mb-1">TODO List</h1>
      </header>

      <TodoForm @add-todo="handleAddTodo" />

      <TodoList
        :todos="todos"
        @toggle-completed="handleToggleCompleted"
        @remove-todo="handleRemoveTodo"
      />

      <footer class="mt-4 text-center text-sm text-gray-500">
        <small>
          本项目仅实现前端逻辑，数据保存在浏览器本地存储（localStorage）中。
        </small>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import TodoForm from './components/TodoForm.vue'
import TodoList from './components/TodoList.vue'
import { loadTodos, saveTodos } from './utils/storage'

const todos = ref([])

onMounted(() => {
  todos.value = loadTodos()
})

watch(
  todos,
  (newTodos) => {
    saveTodos(newTodos)
  },
  { deep: true }
)

function handleAddTodo(todo) {
  todos.value.unshift(todo)
}

function handleToggleCompleted(id) {
  const target = todos.value.find((t) => t.id === id)
  if (target) {
    target.completed = !target.completed
  }
}

function handleRemoveTodo(id) {
  todos.value = todos.value.filter((t) => t.id !== id)
}
</script>
