---
title: 作用域 CSS 不适用于动态添加的内容
impact: HIGH
impactDescription: 以编程方式插入的 DOM 元素不会接收作用域样式数据属性，导致样式静默失败
type: gotcha
tags: [vue3, sfc, scoped-css, dynamic-content, v-html]
---

# 作用域 CSS 不适用于动态添加的内容

**影响：高** - Vue 的作用域 CSS 通过在编译时向元素添加数据属性来工作。在运行时动态添加的内容（通过 `v-html`、JavaScript DOM 操作或第三方库）不会有这些属性，因此作用域样式不会应用。

## 任务清单

- [ ] 对于 `v-html` 内容，使用 `:deep()` 选择器或非作用域样式
- [ ] 避免编程式 DOM 操作；优先使用 Vue 的响应式模板系统
- [ ] 当 DOM 操作不可避免时，使用带有唯一类前缀的全局样式
- [ ] 对于混合静态和动态元素的内容，考虑使用 CSS 模块

**有问题的代码：**
```vue
<script setup>
import { ref } from 'vue'

const htmlContent = ref('<p class="dynamic">这是动态内容</p>')
</script>

<template>
  <div class="container">
    <div v-html="htmlContent"></div>
  </div>
</template>

<style scoped>
/* 错误：不会应用于动态 <p> 元素！ */
.dynamic {
  color: red;
  font-weight: bold;
}
</style>
```

**正确的代码：**
```vue
<script setup>
import { ref } from 'vue'

const htmlContent = ref('<p class="dynamic">这是动态内容</p>')
</script>

<template>
  <div class="container">
    <div v-html="htmlContent"></div>
  </div>
</template>

<style scoped>
/* 正确：为 v-html 内容使用 :deep() */
.container :deep(.dynamic) {
  color: red;
  font-weight: bold;
}
</style>
```

## 为什么会发生这种情况

Vue 作用域 CSS 向以下内容添加唯一的数据属性（例如 `data-v-7ba5bd90`）：
1. 组件模板中的所有元素（在编译时）
2. 所有 CSS 选择器

```html
<!-- Vue 在编译时生成的 -->
<div class="container" data-v-7ba5bd90>
  <div data-v-7ba5bd90>
    <!-- v-html 内容在运行时插入，没有属性 -->
    <p class="dynamic">这是动态内容</p>
  </div>
</div>
```

```css
/* 生成的作用域 CSS */
.dynamic[data-v-7ba5bd90] { color: red; }
/* ^ 不会匹配，因为动态 <p> 没有 data-v-7ba5bd90 */
```

## 替代方案：带有唯一前缀的全局样式

```vue
<script setup>
import { ref } from 'vue'
const htmlContent = ref('<p class="my-component-dynamic">动态文本</p>')
</script>

<template>
  <div class="my-component">
    <div v-html="htmlContent"></div>
  </div>
</template>

<!-- 使用带有唯一前缀的非作用域样式 -->
<style>
.my-component .my-component-dynamic {
  color: red;
}
</style>
```

## 编程式 DOM 操作

当使用操作 DOM 的第三方库时：

```vue
<script setup>
import { ref, onMounted } from 'vue'

const editorRef = ref(null)

onMounted(() => {
  // 注入自己 DOM 元素的第三方编辑器
  initRichEditor(editorRef.value)
})
</script>

<template>
  <div class="editor-wrapper">
    <div ref="editorRef"></div>
  </div>
</template>

<style scoped>
/* 错误：不会到达注入的编辑器元素 */
.editor-toolbar { ... }
.editor-content { ... }
</style>

<style>
/* 正确：通过父类作用域的全局样式 */
.editor-wrapper .editor-toolbar {
  background: #f5f5f5;
}
.editor-wrapper .editor-content {
  padding: 1rem;
}
</style>
```

## 最佳实践：优先使用响应式模板

尽可能使用 Vue 的响应式系统，而不是动态 HTML：

```vue
<script setup>
import { ref } from 'vue'

// 错误：需要特殊样式处理的动态 HTML
const badHtml = ref('<span class="highlight">文本</span>')

// 正确：模板处理的响应式数据
const items = ref([
  { text: '项目 1', isHighlighted: true },
  { text: '项目 2', isHighlighted: false }
])
</script>

<template>
  <!-- 错误 -->
  <div v-html="badHtml"></div>

  <!-- 正确：作用域样式正常工作 -->
  <ul>
    <li
      v-for="item in items"
      :key="item.text"
      :class="{ highlight: item.isHighlighted }"
    >
      {{ item.text }}
    </li>
  </ul>
</template>

<style scoped>
/* 与响应式模板完美配合 */
.highlight {
  background: yellow;
}
</style>
```

## 参考
- [Vue.js 作用域 CSS](https://vuejs.org/api/sfc-css-features.html#scoped-css)
- [GitHub 问题：作用域 CSS 不应用于以编程方式添加的元素](https://github.com/vuejs/vue/issues/7649)
