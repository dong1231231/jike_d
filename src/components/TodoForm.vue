<template>
  <section class="card">
    <h2 class="card-title">新增任务</h2>
    <form class="todo-form" @submit.prevent="handleSubmit" @reset="handleReset">
      <div class="form-row">
        <label for="title">标题 *</label>
        <input
          id="title"
          v-model.trim="form.title"
          type="text"
          placeholder="例如：完成技术笔试题"
          required
        />
      </div>
      <div class="form-row">
        <label for="description">描述（可选）</label>
        <textarea
          id="description"
          v-model.trim="form.description"
          rows="2"
          placeholder="例如：实现 TODO List 前端并写 README"
        ></textarea>
      </div>
      <div class="form-row">
        <label for="category">分类（可选）</label>
        <select id="category" v-model="form.category">
          <option value="">未分类</option>
          <option value="work">工作</option>
          <option value="study">学习</option>
          <option value="life">生活</option>
        </select>
      </div>
      <div class="form-row form-row-inline">
        <div class="form-row">
          <label for="priority">优先级</label>
          <select id="priority" v-model="form.priority">
            <option value="medium">中</option>
            <option value="high">高</option>
            <option value="low">低</option>
          </select>
        </div>
        <div class="form-row">
          <label for="dueDate">截止日期</label>
          <input id="dueDate" type="date" v-model="form.dueDate" />
        </div>
      </div>
      <div class="form-actions">
        <button type="submit" class="btn primary">添加任务</button>
        <button type="reset" class="btn ghost">清空</button>
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

