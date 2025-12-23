<template>
  <div class="app">
    <header class="app-header">
      <h1>TODO List</h1>
    </header>

    <TodoForm @add-todo="handleAddTodo" />

    <TodoList
      :todos="todos"
      @toggle-completed="handleToggleCompleted"
      @remove-todo="handleRemoveTodo"
    />

    <footer class="app-footer">
      <small>
        本项目仅实现前端逻辑，数据保存在浏览器本地存储（localStorage）中。
      </small>
    </footer>
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

