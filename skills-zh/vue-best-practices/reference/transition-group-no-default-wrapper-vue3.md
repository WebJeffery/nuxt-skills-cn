---
title: TransitionGroup 在 Vue 3 中不再渲染默认包装元素
impact: MEDIUM
impactDescription: 从 Vue 2 迁移到 Vue 3 可能会破坏依赖于默认 span 包装器的布局
type: gotcha
tags: [vue3, transition-group, migration, vue2, breaking-change, wrapper-element]
---

# TransitionGroup 在 Vue 3 中不再渲染默认包装元素

**影响: MEDIUM** - 在 Vue 2 中，`<transition-group>` 总是渲染一个包装元素（默认为 `<span>`），但在 Vue 3 中，由于片段支持，它默认不渲染包装元素。这个破坏性更改在从 Vue 2 迁移时可能导致布局问题和样式损坏。

如果你的代码依赖于包装元素进行样式设置或布局，必须在 Vue 3 中显式指定 `tag` 属性。

## 任务清单

- [ ] 从 Vue 2 迁移时，为所有 `<TransitionGroup>` 组件添加显式的 `tag` 属性
- [ ] 审查针对包装元素的 CSS 选择器
- [ ] 更新期望包装元素的父组件样式
- [ ] 考虑在 Vue 3 中是否真的需要包装元素

**Vue 2 行为（默认包装元素）:**
```vue
<template>
  <transition-group name="list">
    <div v-for="item in items" :key="item.id">{{ item }}</div>
  </transition-group>
</template>

<!-- 渲染为: -->
<span>  <!-- Vue 2 中的默认包装器 -->
  <div>Item 1</div>
  <div>Item 2</div>
</span>
```

**Vue 3 行为（默认无包装元素）:**
```vue
<template>
  <TransitionGroup name="list">
    <div v-for="item in items" :key="item.id">{{ item }}</div>
  </TransitionGroup>
</template>

<!-- 渲染为（片段）: -->
<div>Item 1</div>
<div>Item 2</div>
<!-- 没有包装元素! -->
```

**Vue 3 - 显式指定包装器:**
```vue
<template>
  <!-- 使用 tag 属性指定包装元素 -->
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">{{ item }}</li>
  </TransitionGroup>
</template>

<!-- 渲染为: -->
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
</ul>
```

## 迁移场景

### 依赖于包装器的布局

**在 Vue 3 中中断的 Vue 2 代码:**
```vue
<template>
  <transition-group class="grid-container" name="list">
    <div v-for="item in items" :key="item.id">{{ item }}</div>
  </transition-group>
</template>

<style>
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}
</style>
```

在 Vue 3 中，class 没有应用于任何东西，因为没有包装元素。

**为 Vue 3 修复:**
```vue
<template>
  <TransitionGroup class="grid-container" name="list" tag="div">
    <div v-for="item in items" :key="item.id">{{ item }}</div>
  </TransitionGroup>
</template>
```

### 语义化 HTML 列表

**Vue 2:**
```vue
<transition-group tag="ul" name="list">
  <li v-for="item in items" :key="item.id">{{ item }}</li>
</transition-group>
```

**Vue 3（相同的语法，但现在 tag 更重要）:**
```vue
<TransitionGroup tag="ul" name="list">
  <li v-for="item in items" :key="item.id">{{ item }}</li>
</TransitionGroup>
```

### 当你不需要包装器时

Vue 3 的片段支持意味着你可能根本不需要包装器:

```vue
<template>
  <div class="parent-with-styles">
    <!-- 如果父元素处理布局，则不需要 tag -->
    <TransitionGroup name="fade">
      <span v-for="item in items" :key="item.id">{{ item }}</span>
    </TransitionGroup>
  </div>
</template>

<style>
.parent-with-styles {
  display: flex;
  gap: 8px;
}
</style>
```

## DOM 内模板语法

使用 DOM 内模板（不是 SFC）时，记得使用 kebab-case:

```html
<!-- DOM 内模板 -->
<transition-group tag="ul" name="list">
  <li v-for="item in items" :key="item.id">{{ item }}</li>
</transition-group>

<!-- 而不是 -->
<TransitionGroup tag="ul" name="list">  <!-- 在 DOM 模板中不起作用 -->
```

## 参考
- [Vue 3 迁移指南 - TransitionGroup 根元素](https://v3-migration.vuejs.org/breaking-changes/transition-group.html)
- [Vue.js TransitionGroup](https://vuejs.org/guide/built-ins/transition-group.html)
