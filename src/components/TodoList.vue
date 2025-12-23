<template>
  <section class="card">
    <div class="card-header">
      <h2 class="card-title">任务列表</h2>
      <div class="toolbar">
        <input
          type="search"
          placeholder="搜索标题或描述..."
          v-model.trim="search"
        />
        <select v-model="filterStatus">
          <option value="all">全部</option>
          <option value="active">未完成</option>
          <option value="completed">已完成</option>
        </select>
        <select v-model="sortBy">
          <option value="createdAtDesc">按创建时间（新→旧）</option>
          <option value="createdAtAsc">按创建时间（旧→新）</option>
          <option value="priority">按优先级</option>
          <option value="dueDate">按截止日期</option>
        </select>
      </div>
    </div>
    <div class="empty-state" v-if="!visibleTodos.length">
      还没有任务，先在上方添加一个吧。
    </div>
    <ul class="todo-list" v-else>
      <TodoItem
        v-for="todo in visibleTodos"
        :key="todo.id"
        :todo="todo"
        @toggle-completed="handleToggleCompleted"
        @remove-todo="handleRemoveTodo"
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

const emit = defineEmits(['toggle-completed', 'remove-todo'])

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
    return 0
  })

  return result
})

function handleToggleCompleted(id) {
  emit('toggle-completed', id)
}

function handleRemoveTodo(id) {
  emit('remove-todo', id)
}
</script>

