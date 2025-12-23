<template>
  <li class="todo-item">
    <input
      type="checkbox"
      class="todo-checkbox"
      :checked="todo.completed"
      @change="handleToggle"
    />
    <div class="todo-main">
      <div class="todo-title-row">
        <span class="todo-title" :class="{ completed: todo.completed }">
          {{ todo.title }}
        </span>
      </div>
      <div class="todo-meta">
        <span v-if="todo.category" class="badge category">
          {{ categoryLabel(todo.category) }}
        </span>
        <span class="badge" :class="'priority-' + todo.priority">
          {{ priorityLabel(todo.priority) }}
        </span>
        <span v-if="todo.dueDate" class="badge due">
          截止 {{ todo.dueDate }}
        </span>
        <span class="badge" style="background-color: #f3f4f6; color: #4b5563">
          创建于 {{ formatCreatedAt(todo.createdAt) }}
        </span>
      </div>
      <div class="todo-description">
        {{ todo.description || '（无描述）' }}
      </div>
    </div>
    <div class="todo-actions">
      <button class="btn icon danger" @click="handleDelete">
        删除
      </button>
    </div>
  </li>
</template>

<script setup>
const props = defineProps({
  todo: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['toggle-completed', 'remove-todo'])

function handleToggle() {
  emit('toggle-completed', props.todo.id)
}

function handleDelete() {
  if (!confirm('确认删除该任务吗？')) return
  emit('remove-todo', props.todo.id)
}

function categoryLabel(value) {
  const map = {
    work: '工作',
    study: '学习',
    life: '生活',
  }
  return map[value] || value
}

function priorityLabel(value) {
  const map = {
    high: '高优先级',
    medium: '中优先级',
    low: '低优先级',
  }
  return map[value] || value
}

function formatCreatedAt(ts) {
  const d = new Date(ts)
  const dateStr = [
    d.getFullYear(),
    String(d.getMonth() + 1).padStart(2, '0'),
    String(d.getDate()).padStart(2, '0'),
  ].join('-')
  const timeStr = [
    String(d.getHours()).padStart(2, '0'),
    String(d.getMinutes()).padStart(2, '0'),
  ].join(':')
  return dateStr + ' ' + timeStr
}
</script>

