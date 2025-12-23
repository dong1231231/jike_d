<template>
  <li
    class="flex gap-2.5 p-2.5 rounded-xl border border-gray-200 bg-gray-50 items-start transition-all hover:bg-white hover:shadow-lg hover:-translate-y-0.5 hover:border-indigo-200"
  >
    <input
      type="checkbox"
      :checked="todo.completed"
      @change="handleToggle"
      class="mt-1"
    />
    <div class="flex-1">
      <div class="flex items-center gap-1.5 mb-1">
        <span
          class="font-semibold text-base"
          :class="{ 'line-through text-gray-400': todo.completed }"
        >
          {{ todo.title }}
        </span>
      </div>
      <div class="flex flex-wrap gap-1 mb-0.5">
        <span
          v-if="todo.category"
          class="inline-flex items-center gap-1 rounded-full px-2 py-0.5 text-xs bg-indigo-50 text-indigo-700"
        >
          {{ categoryLabel(todo.category) }}
        </span>
        <span
          class="inline-flex items-center gap-1 rounded-full px-2 py-0.5 text-xs"
          :class="{
            'bg-red-50 text-red-700': todo.priority === 'high',
            'bg-yellow-50 text-yellow-700': todo.priority === 'medium',
            'bg-green-50 text-green-700': todo.priority === 'low',
          }"
        >
          {{ priorityLabel(todo.priority) }}
        </span>
        <span
          v-if="todo.dueDate"
          class="inline-flex items-center gap-1 rounded-full px-2 py-0.5 text-xs bg-blue-50 text-blue-700"
        >
          截止 {{ todo.dueDate }}
        </span>
        <span
          class="inline-flex items-center gap-1 rounded-full px-2 py-0.5 text-xs bg-gray-100 text-gray-600"
        >
          创建于 {{ formatCreatedAt(todo.createdAt) }}
        </span>
      </div>
      <div class="text-sm text-gray-600">
        {{ todo.description || '（无描述）' }}
      </div>
    </div>
    <div class="flex flex-col gap-1">
      <button
        class="px-2 py-1 rounded-full text-xs bg-red-50 text-red-700 border border-red-200 hover:bg-red-100 transition-all cursor-pointer"
        @click="handleDelete"
      >
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
