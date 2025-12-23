<template>
  <section class="bg-white rounded-2xl p-5 shadow-lg border border-slate-200 mb-4">
    <div class="flex justify-between items-center gap-3 mb-3">
      <h2 class="text-lg font-semibold">任务列表</h2>
      <div class="flex flex-wrap gap-2">
        <input
          type="search"
          placeholder="搜索标题或描述..."
          v-model.trim="search"
          class="px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white min-w-[180px]"
        />
        <select
          v-model="filterStatus"
          class="px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
        >
          <option value="all">全部</option>
          <option value="active">未完成</option>
          <option value="completed">已完成</option>
        </select>
        <select
          v-model="sortBy"
          class="px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
        >
          <option value="createdAtDesc">按创建时间（新→旧）</option>
          <option value="createdAtAsc">按创建时间（旧→新）</option>
          <option value="priority">按优先级</option>
          <option value="dueDate">按截止日期</option>
        </select>
      </div>
    </div>
    <div v-if="!visibleTodos.length" class="py-3 px-2.5 text-sm text-gray-400">
      还没有任务，先在上方添加一个吧。
    </div>
    <ul v-else class="flex flex-col gap-2 list-none">
      <TodoItem
        v-for="todo in visibleTodos"
        :key="todo.id"
        :todo="todo"
        @toggle-completed="handleToggleCompleted"
        @remove-todo="handleRemoveTodo"
        @update-todo="handleUpdateTodo"
      />
    </ul>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue'
import TodoItem from './TodoItem.vue'

const props = defineProps({
  todos: {
    type: Array,
    required: true,
  },
})

const emit = defineEmits(['toggle-completed', 'remove-todo', 'update-todo'])

const search = ref('')
const filterStatus = ref('all')
const sortBy = ref('createdAtDesc')

const visibleTodos = computed(() => {
  const keyword = search.value.trim().toLowerCase()
  let result = props.todos.slice()

  if (keyword) {
    result = result.filter((t) => {
      return (
        t.title.toLowerCase().includes(keyword) ||
        (t.description && t.description.toLowerCase().includes(keyword))
      )
    })
  }

  if (filterStatus.value === 'active') {
    result = result.filter((t) => !t.completed)
  } else if (filterStatus.value === 'completed') {
    result = result.filter((t) => t.completed)
  }

  result.sort((a, b) => {
    if (sortBy.value === 'createdAtAsc') {
      return a.createdAt - b.createdAt
    }
    if (sortBy.value === 'createdAtDesc') {
      return b.createdAt - a.createdAt
    }
    if (sortBy.value === 'priority') {
      const order = { high: 0, medium: 1, low: 2 }
      return order[a.priority] - order[b.priority]
    }
    if (sortBy.value === 'dueDate') {
      if (!a.dueDate && !b.dueDate) return 0
      if (!a.dueDate) return 1
      if (!b.dueDate) return -1
      return a.dueDate.localeCompare(b.dueDate)
    }
  })

  return result
})

function handleToggleCompleted(id) {
  emit('toggle-completed', id)
}

function handleRemoveTodo(id) {
  emit('remove-todo', id)
}

function handleUpdateTodo(payload) {
  emit('update-todo', payload)
}
</script>
