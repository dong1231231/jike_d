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

    <div
      v-if="activeReminder"
      class="fixed inset-0 bg-black/40 backdrop-blur-sm z-50 flex items-center justify-center px-4"
    >
      <div class="bg-white rounded-2xl shadow-2xl max-w-md w-full p-5 border border-slate-200">
        <div class="flex items-start gap-3">
          <div class="flex h-10 w-10 items-center justify-center rounded-full bg-amber-100 text-amber-700 text-lg font-semibold">
            ⏰
          </div>
          <div class="flex-1">
            <h3 class="text-lg font-semibold text-slate-900 mb-1">到期提醒</h3>
            <p class="text-sm text-slate-700 mb-2">
              “{{ activeReminder.title }}” 将在
              <span class="font-semibold text-slate-900">{{ formatDateTime(activeReminder.dueDate) }}</span>
              到期
            </p>
            <p class="text-sm text-slate-700 mb-2">（提前 2 小时提醒）</p>
            <p class="text-xs text-slate-500">点击“我知道了”确认，确认后不再重复提醒。</p>
          </div>
        </div>
        <div class="flex justify-end gap-2 mt-4">
          <button
            class="px-4 py-2 rounded-lg text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 transition-all"
            @click="confirmReminder"
          >
            我知道了
          </button>
        </div>
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
let reminderTimer = null
const reminderIntervalMs = 10 * 1000
const activeReminder = ref(null)

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
  if (reminderTimer) {
    clearInterval(reminderTimer)
  }
  document.removeEventListener('visibilitychange', handleVisibilityChange)
})

onMounted(() => {
  todos.value = loadTodosWithDefault()
  runReminderCheck()
  reminderTimer = setInterval(runReminderCheck, reminderIntervalMs)
  document.addEventListener('visibilitychange', handleVisibilityChange)
})

watch(
  todos,
  (newTodos) => {
    saveTodos(newTodos)
  },
  { deep: true }
)

function handleAddTodo(todo) {
  todos.value.unshift({ ...todo, reminded: false })
  showToast('添加成功')
  runReminderCheck()
}

function handleToggleCompleted(id) {
  const target = todos.value.find((t) => t.id === id)
  if (target) {
    target.completed = !target.completed
    if (target.completed) {
      target.reminded = true
    }
  }
  runReminderCheck()
}

function handleRemoveTodo(id) {
  todos.value = todos.value.filter((t) => t.id !== id)
}

function handleUpdateTodo(payload) {
  const idx = todos.value.findIndex((t) => t.id === payload.id)
  if (idx !== -1) {
    const prev = todos.value[idx]
    const dueChanged = (prev.dueDate || '') !== (payload.dueDate || '')
    const updated = {
      ...prev,
      ...payload,
    }
    if (dueChanged) {
      updated.reminded = false
    }
    todos.value[idx] = updated
    showToast('保存成功')
    runReminderCheck()
  }
}

function handleVisibilityChange() {
  if (document.visibilityState === 'visible') {
    runReminderCheck()
  }
}

function loadTodosWithDefault() {
  return loadTodos().map((t) => ({
    reminded: false,
    ...t,
  }))
}

function runReminderCheck() {
  if (activeReminder.value) return
  const now = Date.now()
  const twoHours = 2 * 60 * 60 * 1000
  for (const t of todos.value) {
    if (!t.completed && t.dueDate && !t.reminded) {
      const dueTs = Date.parse(t.dueDate)
      if (!Number.isNaN(dueTs)) {
        const remindAt = dueTs - twoHours
        if (now >= remindAt && now < dueTs) {
          activeReminder.value = {
            id: t.id,
            title: t.title,
            dueDate: t.dueDate,
          }
          break
        }
      }
    }
  }
}

function confirmReminder() {
  if (!activeReminder.value) return
  const id = activeReminder.value.id
  todos.value = todos.value.map((t) =>
    t.id === id ? { ...t, reminded: true } : t
  )
  saveTodos(todos.value)
  activeReminder.value = null
  showToast('提醒已确认')
  // 立即检查是否还有下一个需要提醒的任务
  runReminderCheck()
}

function formatDateTime(str) {
  const d = new Date(str)
  if (Number.isNaN(d.getTime())) return str
  const dateStr = [
    d.getFullYear(),
    String(d.getMonth() + 1).padStart(2, '0'),
    String(d.getDate()).padStart(2, '0'),
  ].join('-')
  const timeStr = [
    String(d.getHours()).padStart(2, '0'),
    String(d.getMinutes()).padStart(2, '0'),
  ].join(':')
  return `${dateStr} ${timeStr}`
}
</script>
