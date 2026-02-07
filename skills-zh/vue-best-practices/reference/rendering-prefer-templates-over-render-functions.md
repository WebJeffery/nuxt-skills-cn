---
title: 为编译器优化优先使用模板而非渲染函数
impact: MEDIUM
impactDescription: 渲染函数绕过 Vue 的编译时优化，导致更新期间更多工作
type: best-practice
tags: [vue3, rendering, performance, templates, render-functions, optimization]
---

# 为编译器优化优先使用模板而非渲染函数

**影响：中** - Vue 的模板编译器应用自动优化（静态提升、补丁标志、树扁平化），这些对于手写渲染函数是不可能的。使用渲染函数意味着每个 vnode 必须在每次更新时遍历和 diff，即使没有任何变化。

仅当你真正需要完整的 JavaScript 功能来处理高度动态的组件逻辑时，才使用渲染函数。

## 任务清单

- [ ] 默认所有组件使用模板
- [ ] 仅对高度动态的渲染逻辑使用渲染函数（例如，动态标签选择）
- [ ] 如果使用渲染函数，请了解你选择退出编译时优化
- [ ] 如果你需要渲染函数功能但更好的可读性，请考虑 JSX

## 为什么模板更快

Vue 的编译器分析模板并生成优化的渲染函数，具有：

1. **静态提升**：静态元素创建一次并在重新渲染之间重用
2. **补丁标志**：动态绑定被标记，以便 Vue 仅检查可能变化的内容
3. **树扁平化**：仅动态节点在协调期间被遍历

**错误：**
```vue
<script setup>
import { h, ref } from 'vue'

const count = ref(0)
const items = ref(['a', 'b', 'c'])

// 错误：手写渲染函数 - 没有编译时优化
// 每次渲染：所有 vnode 重新创建，所有节点 diff
const render = () => h('div', [
  h('header', { class: 'static-header' }, '我的应用'),  // 静态但每次渲染重新创建
  h('p', `计数: ${count.value}`),
  h('ul', items.value.map(item => h('li', { key: item }, item)))
])
</script>
```

**正确：**
```vue
<template>
  <div>
    <!-- 正确：静态页眉提升，从不重新创建或 diff -->
    <header class="static-header">我的应用</header>

    <!-- 正确：补丁标志仅将文本标记为动态 -->
    <p>计数: {{ count }}</p>

    <!-- 正确：仅列表项作为动态跟踪 -->
    <ul>
      <li v-for="item in items" :key="item">{{ item }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)
const items = ref(['a', 'b', 'c'])
</script>
```

## 渲染函数何时适用

在以下情况下使用渲染函数：
- 渲染逻辑高度动态（基于数据的动态组件类型）
- 构建具有复杂插槽转发的可重用库组件
- 需要模板无法表达的编程控制

```js
// 可接受：基于数据的动态组件类型
function render() {
  return h(props.level > 2 ? 'h3' : 'h' + props.level, slots.default?.())
}

// 可接受：具有动态子元素操作的复杂库组件
function render() {
  const children = slots.default?.() || []
  return h('div', children.filter(shouldRender).map(wrapChild))
}
```

## 你失去的编译器优化

| 优化 | 使用模板 | 使用渲染函数 |
|--------------|---------------|----------------------|
| 静态提升 | 自动 | 手动（你必须做） |
| 补丁标志 | 自动 | 无 |
| 树扁平化 | 自动 | 无 |
| SSR 水合优化 | 完整 | 部分 |
| 块级快速路径 | 是 | 否 |

## 如果你必须使用渲染函数

```js
import { h, ref } from 'vue'

// 在组件外部手动提升静态 vnode
const staticHeader = h('header', { class: 'static' }, '标题')

export default {
  setup() {
    const count = ref(0)

    return () => h('div', [
      staticHeader,  // 重用，不重新创建
      h('p', `计数: ${count.value}`)
    ])
  }
}
```

## 参考
- [Vue.js 渲染机制 - 模板与渲染函数](https://vuejs.org/guide/extras/rendering-mechanism.html#templates-vs-render-functions)
- [Vue.js 渲染函数和 JSX](https://vuejs.org/guide/extras/render-function.html)
