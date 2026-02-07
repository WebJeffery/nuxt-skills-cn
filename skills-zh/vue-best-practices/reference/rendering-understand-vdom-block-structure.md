---
title: 理解 v-if 和 v-for 如何创建新的 VDOM 块
impact: LOW
impactDescription: Vue 的基于块的优化意味着条件和循环结构创建单独的优化边界
type: concept
tags: [vue3, rendering, vdom, v-if, v-for, blocks, internals]
---

# 理解 v-if 和 v-for 如何创建新的 VDOM 块

**影响：低** - 这是关于 Vue 内部的信息性概念。理解块有助于你推理渲染性能，但 Vue 会自动处理这个问题。

Vue 的编译器创建"块" - 具有稳定结构的模板部分。块边界在 `v-if` 和 `v-for` 指令处创建，其中内部结构可能在运行时变化。

## 关键概念

### 什么是块？

块是具有以下特征的模板部分：
1. 具有稳定的内部结构（相同的元素类型、相同的嵌套）
2. 仅跟踪其动态后代节点（而非所有后代）
3. 创建扁平化数组以进行高效协调

### 块边界

在以下位置创建新块：
- **每个组件模板的根**
- **`v-if` / `v-else` / `v-else-if`** - 不同的分支可能具有不同的结构
- **`v-for`** - 每次迭代可能不同

```vue
<template>
  <!-- 根块从这里开始 -->
  <div>
    <header>静态页眉</header>  <!-- 提升，不跟踪 -->

    <p>{{ message }}</p>  <!-- 动态，在根块中跟踪 -->

    <!-- v-if 创建嵌套块 -->
    <section v-if="showDetails">
      <span>{{ detail1 }}</span>  <!-- 在 v-if 块中跟踪 -->
      <span>{{ detail2 }}</span>  <!-- 在 v-if 块中跟踪 -->
    </section>

    <!-- v-for 为每个项目创建块 -->
    <div v-for="item in items" :key="item.id">
      <span :class="item.type">{{ item.name }}</span>
    </div>

    <footer>静态页脚</footer>  <!-- 提升，不跟踪 -->
  </div>
</template>
```

### 块如何优化渲染

没有块（纯运行时 diff）：
```
重新渲染遍历整个树：
div
  header (检查)
    "静态页眉" (检查)
  p (检查)
    message (检查)
  section (检查)
    span (检查)
    span (检查)
  div*N (检查每个)
    span*N (检查每个)
  footer (检查)
    "静态页脚" (检查)
```

有块（Vue 的方法）：
```
重新渲染仅遍历动态节点：
根块：
  - 带有 {{ message }} 的 p
  - v-if 块引用
  - v-for 块引用

v-if 块（仅当可见时）：
  - 带有 {{ detail1 }} 的 span
  - 带有 {{ detail2 }} 的 span

每个 v-for 块：
  - 带有 :class 和 {{ item.name }} 的 span
```

## 为什么这很重要

1. **跳过静态内容**：页眉、页脚和未更改的元素在更新期间永远不会被遍历

2. **高效列表更新**：当列表项更改时，Vue 仅 diff 该项的块，而不是整个列表

3. **分支切换已优化**：当 `v-if` 切换时，Vue 知道替换整个块而不是 diff 不兼容的结构

## 块跟踪示例

```vue
<template>
  <div>
    <!-- 这些静态元素：提升一次，从不 diff -->
    <nav class="sidebar">导航</nav>

    <!-- 这个动态绑定：在根块中跟踪 -->
    <h1>{{ title }}</h1>

    <!-- v-for：每个项目都是自己的块 -->
    <article v-for="post in posts" :key="post.id">
      <h2>{{ post.title }}</h2>  <!-- 在项目的块中跟踪 -->
      <p>{{ post.excerpt }}</p>   <!-- 在项目的块中跟踪 -->
    </article>

    <!-- v-if：单独的块，仅当可见时跟踪 -->
    <aside v-if="showAside">
      <span>{{ asideContent }}</span>
    </aside>
  </div>
</template>
```

当 `posts[2].title` 更改时：
- Vue 找到第 3 个 v-for 块
- 仅 diff 该块的跟踪动态节点
- 跳过所有其他帖子、导航、侧边栏

## 你不需要做任何事情

使用模板时，这种优化是自动的。理解块有助于你：
- 推理为什么更新很快
- 理解 Vue DevTools 性能面板输出
- 欣赏为什么推荐模板而不是渲染函数

## 参考
- [Vue.js 渲染机制 - 树扁平化](https://vuejs.org/guide/extras/rendering-mechanism.html#tree-flattening)
- [Vue.js 渲染机制 - 编译器通知的虚拟 DOM](https://vuejs.org/guide/extras/rendering-mechanism.html#compiler-informed-virtual-dom)
