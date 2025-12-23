# TODO List 应用 - Vue 3 实现

根据 [iftechio/coding-challenge-2025](https://github.com/iftechio/coding-challenge-2025) 要求实现的前端 TODO List 应用。

## 技术栈

- **Vue 3** (Composition API)
- **Vite** (构建工具)
- **原生 CSS** (样式)

## 功能实现

### 必须完成的功能 ✅

1. ✅ 添加待办事项（包含标题，描述可选）
2. ✅ 删除待办事项
3. ✅ 标记待办事项完成/未完成
4. ✅ 查看待办事项列表

### 基础扩展功能 ✅

1. ✅ 数据持久化（使用浏览器 localStorage）
2. ✅ 任务分类（工作/学习/生活）
3. ✅ 任务排序（按创建时间、优先级、截止日期）

### 进阶挑战功能 ✅

- ✅ 搜索功能（按标题或描述关键字搜索）
- ✅ 状态过滤（全部/未完成/已完成）

## 项目结构

```
.
├── index.html          # 入口 HTML
├── package.json        # 项目配置
├── vite.config.js      # Vite 配置
├── src/
│   ├── main.js         # 应用入口
│   ├── App.vue         # 根组件
│   ├── style.css       # 全局样式
│   ├── components/     # 组件目录
│   │   ├── TodoForm.vue    # 任务表单组件
│   │   ├── TodoList.vue    # 任务列表组件
│   │   └── TodoItem.vue    # 任务项组件
│   └── utils/          # 工具函数
│       └── storage.js      # 本地存储工具
└── README.md           # 项目说明
```

## 运行方式

### 1. 安装依赖

```bash
npm install
```

或使用其他包管理器：

```bash
pnpm install
# 或
yarn install
```

### 2. 启动开发服务器

```bash
npm run dev
```

启动后，在浏览器中访问控制台显示的本地地址（通常是 `http://localhost:5173`）。

### 3. 构建生产版本

```bash
npm run build
```

构建产物将输出到 `dist` 目录。

### 4. 预览生产构建

```bash
npm run preview
```

## 组件说明

- **App.vue**: 根组件，管理全局状态（todos 列表）
- **TodoForm.vue**: 任务表单组件，处理新增任务
- **TodoList.vue**: 任务列表组件，处理搜索、过滤、排序
- **TodoItem.vue**: 单个任务项组件，展示任务详情

## 数据存储

所有数据保存在浏览器的 `localStorage` 中，键名为 `iftechio_todos_v1`。刷新页面后数据不会丢失。

## 开发说明

本项目使用 Vue 3 的 Composition API 和 `<script setup>` 语法，代码结构清晰，易于维护和扩展。

