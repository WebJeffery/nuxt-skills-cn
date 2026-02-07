---
title: 使用构建工具的单文件组件
impact: MEDIUM
impactDescription: SFC 为 Vue 应用程序提供更好的 DX、作用域样式和工具支持
type: efficiency
tags: [vue3, sfc, single-file-components, build-tools, vite]
---

# 使用构建工具的单文件组件

**影响：中等** - 单文件组件（SFC）是在使用构建设置时编写 Vue 组件的推荐方式。它们提供更好的组织、作用域样式和卓越的工具支持。

SFC（`.vue` 文件）将模板、逻辑和样式封装在单个文件中。虽然 Vue 可以在没有构建工具的情况下工作，但 SFC 解锁了框架的全部功能，包括 `<script setup>`、作用域 CSS 和更好的 IDE 支持。

## 任务清单

- [ ] 当你有构建设置（Vite、Vue CLI）时，为组件使用 `.vue` 文件
- [ ] 对于组合式 API，优先使用 `<script setup>` 语法 - 更少的样板代码
- [ ] 使用 `<style scoped>` 防止 CSS 在组件之间泄漏
- [ ] 为 Vue 3 配置 IDE（使用 Volar 扩展，而不是 Vetur）

**错误：**
```javascript
// 在构建项目中不使用 SFC 定义组件
// 失去了作用域样式、模板编译和工具的好处

// component.js
export default {
  template: `
    <div class="container">
      <h1>{{ title }}</h1>
      <button @click="handleClick">Click me</button>
    </div>
  `,
  data() {
    return { title: 'Hello' }
  },
  methods: {
    handleClick() { /* ... */ }
  }
}
```

**正确：**
```vue
<!-- Component.vue - 单文件组件 -->
<script setup>
import { ref } from 'vue'

const title = ref('Hello')

function handleClick() {
  // ...
}
</script>

<template>
  <div class="container">
    <h1>{{ title }}</h1>
    <button @click="handleClick">Click me</button>
  </div>
</template>

<style scoped>
/* 样式仅应用于此组件 */
.container {
  padding: 1rem;
}

h1 {
  color: #42b883;
}
</style>
```

## SFC 的好处

| 功能 | 没有 SFC | 有 SFC |
|---------|-------------|----------|
| 模板语法高亮 | 否 | 是 |
| 作用域 CSS | 手动（CSS 模块） | 内置 |
| TypeScript 支持 | 基本 | 通过 Volar 完整支持 |
| 热模块替换 | 有限 | 完全支持 |
| 构建时优化 | 无 | 模板编译 |
| IDE 自动完成 | 有限 | 完全支持 |

## 何时不使用 SFC

```html
<!-- 渐进式增强 - 向现有 HTML 添加 Vue -->
<!-- 这里不需要 SFC -->
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>

<script type="module">
import { createApp, ref } from 'vue'

createApp({
  setup() {
    return { count: ref(0) }
  }
}).mount('#app')
</script>
```

## 参考
- [Vue.js 介绍 - 单文件组件](https://vuejs.org/guide/introduction.html#single-file-components)
- [SFC 语法规范](https://vuejs.org/api/sfc-spec.html)
