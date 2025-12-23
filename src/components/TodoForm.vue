<template>
  <section class="bg-white rounded-2xl p-5 shadow-lg border border-slate-200 mb-4">
    <h2 class="text-lg font-semibold mb-3">新增任务</h2>
    <form class="flex flex-col gap-2.5" @submit.prevent="handleSubmit" @reset="handleReset">
      <div class="w-full">
        <label for="title" class="block text-sm text-gray-600 mb-1">
          标题
          <span class="text-red-500">*</span>
        </label>
        <input
          id="title"
          v-model.trim="form.title"
          type="text"
          placeholder="例如：考研"
          required
          class="w-full px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
        />
      </div>
      <div class="w-full">
        <label for="description" class="block text-sm text-gray-600 mb-1">描述（可选）</label>
        <textarea
          id="description"
          v-model.trim="form.description"
          rows="2"
          placeholder="例如：准备准考证、身份证、笔"
          class="w-full px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white resize-none"
        ></textarea>
      </div>
      <div class="w-full">
        <label for="category" class="block text-sm text-gray-600 mb-1">分类（可选）</label>
        <select
          id="category"
          v-model="form.category"
          class="w-full px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
        >
          <option value="">未分类</option>
          <option value="work">工作</option>
          <option value="study">学习</option>
          <option value="life">生活</option>
        </select>
      </div>
      <div class="flex gap-2 flex-wrap">
        <div class="flex-1 min-w-[160px]">
          <label for="priority" class="block text-sm text-gray-600 mb-1">优先级</label>
          <select
            id="priority"
            v-model="form.priority"
            class="w-full px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
          >
            <option value="medium">中</option>
            <option value="high">高</option>
            <option value="low">低</option>
          </select>
        </div>
        <div class="flex-1 min-w-[160px]">
          <label for="dueDate" class="block text-sm text-gray-600 mb-1">截止日期</label>
          <input
            id="dueDate"
            type="date"
            v-model="form.dueDate"
            class="w-full px-2.5 py-2 rounded-lg border border-gray-300 text-sm outline-none transition-all bg-gray-50 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 focus:bg-white"
          />
        </div>
      </div>
      <div class="flex justify-end gap-2 mt-1">
        <button
          type="submit"
          class="px-4 py-2 rounded-full text-sm font-medium text-white bg-gradient-to-r from-indigo-600 to-indigo-500 shadow-lg shadow-indigo-500/30 hover:from-indigo-700 hover:to-indigo-600 hover:-translate-y-0.5 transition-all cursor-pointer"
        >
          添加任务
        </button>
        <button
          type="reset"
          class="px-4 py-2 rounded-full text-sm font-medium text-gray-600 bg-gray-50 border border-gray-200 hover:bg-gray-100 transition-all cursor-pointer"
        >
          清空
        </button>
      </div>
    </form>
  </section>
</template>

<script setup>
import { reactive } from 'vue'

const emit = defineEmits(['add-todo'])

const form = reactive({
  title: '',
  description: '',
  category: '',
  priority: 'medium',
  dueDate: '',
})

function handleSubmit() {
  if (!form.title.trim()) {
    alert('标题不能为空')
    return
  }

  const now = Date.now()
  const todo = {
    id: String(now) + '-' + Math.random().toString(16).slice(2),
    title: form.title.trim(),
    description: form.description.trim(),
    category: form.category,
    priority: form.priority || 'medium',
    dueDate: form.dueDate || null,
    completed: false,
    createdAt: now,
  }

  emit('add-todo', todo)
  handleReset()
}

function handleReset() {
  form.title = ''
  form.description = ''
  form.category = ''
  form.priority = 'medium'
  form.dueDate = ''
}
</script>
