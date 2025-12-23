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
      <template v-if="!editing">
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
          截止 {{ formatDateTime(todo.dueDate) }}
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
      </template>

      <template v-else>
        <div class="flex flex-col gap-2">
          <input
            v-model.trim="draft.title"
            type="text"
            placeholder="标题"
            class="w-full px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
          />
          <textarea
            v-model.trim="draft.description"
            rows="2"
            placeholder="描述"
            class="w-full px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white resize-none"
          ></textarea>
          <div class="flex gap-2 flex-wrap">
            <select
              v-model="draft.category"
              class="flex-1 min-w-[140px] px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
            >
              <option value="">未分类</option>
              <option value="work">工作</option>
              <option value="study">学习</option>
              <option value="life">生活</option>
            </select>
            <select
              v-model="draft.priority"
              class="flex-1 min-w-[140px] px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
            >
              <option value="medium">中</option>
              <option value="high">高</option>
              <option value="low">低</option>
            </select>
            <input
              v-model="draft.dueDate"
              type="datetime-local"
              class="flex-1 min-w-[140px] px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
            />
          </div>
        </div>
      </template>
    </div>
    <div class="flex flex-col gap-1">
      <template v-if="!editing">
        <button
          class="px-2 py-1 rounded-full text-xs bg-indigo-50 text-indigo-700 border border-indigo-200 hover:bg-indigo-100 transition-all cursor-pointer"
          @click="enterEdit"
        >
          编辑
        </button>
        <button
          class="px-2 py-1 rounded-full text-xs bg-red-50 text-red-700 border border-red-200 hover:bg-red-100 transition-all cursor-pointer"
          @click="handleDelete"
        >
          删除
        </button>
      </template>
      <template v-else>
        <button
          class="px-2 py-1 rounded-full text-xs bg-green-50 text-green-700 border border-green-200 hover:bg-green-100 transition-all cursor-pointer"
          @click="saveEdit"
        >
          保存
        </button>
        <button
          class="px-2 py-1 rounded-full text-xs bg-gray-50 text-gray-600 border border-gray-200 hover:bg-gray-100 transition-all cursor-pointer"
          @click="cancelEdit"
        >
          取消
        </button>
      </template>
    </div>
  </li>
</template>

<script setup>
import { reactive, ref, watch } from 'vue'

const props = defineProps({
  todo: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['toggle-completed', 'remove-todo', 'update-todo'])

const editing = ref(false)
const draft = reactive({
  title: '',
  description: '',
  category: '',
  priority: 'medium',
  dueDate: '',
})

function syncDraft() {
  draft.title = props.todo.title || ''
  draft.description = props.todo.description || ''
  draft.category = props.todo.category || ''
  draft.priority = props.todo.priority || 'medium'
  draft.dueDate = props.todo.dueDate || ''
}

watch(
  () => props.todo,
  () => {
    if (!editing.value) {
      syncDraft()
    }
  },
  { deep: true, immediate: true }
)

function handleToggle() {
  emit('toggle-completed', props.todo.id)
}

function handleDelete() {
  if (!confirm('确认删除该任务吗？')) return
  emit('remove-todo', props.todo.id)
}

function enterEdit() {
  syncDraft()
  editing.value = true
}

function cancelEdit() {
  editing.value = false
  syncDraft()
}

function saveEdit() {
  if (!draft.title.trim()) {
    alert('标题不能为空')
    return
  }
  emit('update-todo', {
    id: props.todo.id,
    title: draft.title.trim(),
    description: draft.description.trim(),
    category: draft.category,
    priority: draft.priority || 'medium',
    dueDate: draft.dueDate || null,
  })
  editing.value = false
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

function formatDateTime(str) {
  if (!str) return ''
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
  return dateStr + ' ' + timeStr
}
</script>
