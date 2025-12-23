<template>
  <div class="min-h-screen bg-gradient-to-br from-indigo-50 to-gray-50 relative">
    <div
      v-if="toastMessage"
      class="fixed top-4 inset-x-0 flex justify-center px-4 z-50"
    >
      <div
        class="flex items-center gap-2 bg-green-50 text-green-700 border border-green-200 px-4 py-2 rounded-full shadow-lg shadow-green-200"
      >
        <span class="text-sm font-medium">{{ toastMessage }}</span>
      </div>
    </div>

    <div class="max-w-4xl mx-auto px-4 py-6 pb-10">
      <header class="text-center mb-6">
        <h1 class="text-3xl font-bold mb-1">TODO List</h1>
      </header>

      <TodoForm @add-todo="handleAddTodo" />

      <TodoList
        :todos="todos"
        @toggle-completed="handleToggleCompleted"
        @remove-todo="handleRemoveTodo"
        @update-todo="handleUpdateTodo"
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
import { ref, onMounted, watch, onBeforeUnmount } from 'vue'
import TodoForm from './components/TodoForm.vue'
import TodoList from './components/TodoList.vue'
import { loadTodos, saveTodos } from './utils/storage'

const todos = ref([])
const toastMessage = ref('')
let toastTimer = null

function showToast(message, duration = 2000) {
  if (toastTimer) {
    clearTimeout(toastTimer)
  }
  toastMessage.value = message
  toastTimer = setTimeout(() => {
    toastMessage.value = ''
    toastTimer = null
  }, duration)
}

onBeforeUnmount(() => {
  if (toastTimer) {
    clearTimeout(toastTimer)
  }
})

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
  showToast('添加成功')
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

function handleUpdateTodo(payload) {
  const idx = todos.value.findIndex((t) => t.id === payload.id)
  if (idx !== -1) {
    todos.value[idx] = {
      ...todos.value[idx],
      ...payload,
    }
    showToast('保存成功')
  }
}
</script>
