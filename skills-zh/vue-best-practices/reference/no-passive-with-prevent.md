---
title: 永远不要同时使用 .passive 和 .prevent
impact: HIGH
impactDescription: 冲突的修饰符导致 .prevent 被忽略并触发浏览器警告
type: gotcha
tags: [vue3, events, modifiers, scroll, touch, performance]
---

# 永远不要同时使用 .passive 和 .prevent

**影响：高** - `.passive` 修饰符告诉浏览器你不会调用 `preventDefault()`，而 `.prevent` 正是这样做的。同时使用它们会导致 `.prevent` 被忽略并触发浏览器控制台警告。这是一个逻辑矛盾，导致事件处理中断。

## 任务清单

- [ ] 永远不要在同一事件上组合 `.passive` 和 `.prevent`
- [ ] 对你想要更好性能的滚动/触摸事件使用 `.passive`
- [ ] 当你需要停止默认浏览器操作时使用 `.prevent`
- [ ] 如果你需要条件性阻止，在没有 `.passive` 的情况下在 JavaScript 中处理它

**错误：**
```html
<!-- 错误：冲突的修饰符 -->
<template>
  <div @scroll.passive.prevent="handleScroll">
    <!-- .prevent 将被忽略 -->
    <!-- 浏览器显示警告 -->
  </div>
</template>
```

```html
<!-- 错误：在触摸事件上 -->
<template>
  <div @touchstart.passive.prevent="handleTouch">
    <!-- 无法阻止默认 - passive 已承诺不会 -->
  </div>
</template>
```

```html
<!-- 错误：在滚轮事件上 -->
<template>
  <div @wheel.passive.prevent="handleWheel">
    <!-- 中断：尽管有 .prevent 仍会滚动 -->
  </div>
</template>
```

**正确：**
```html
<!-- 正确：使用 .passive 以提高性能（不需要阻止） -->
<template>
  <div @scroll.passive="handleScroll">
    <!-- 适合不阻塞的滚动跟踪 -->
  </div>
</template>
```

```html
<!-- 正确：当你需要阻止默认时使用 .prevent -->
<template>
  <form @submit.prevent="handleSubmit">
    <!-- 正确阻止表单提交 -->
  </form>
</template>
```

```html
<!-- 正确：对于需要阻止的触摸事件 -->
<template>
  <div @touchmove="handleTouchMove">
    <!-- 在 JS 中条件性处理阻止 -->
  </div>
</template>

<script setup>
function handleTouchMove(event) {
  if (shouldPreventScroll.value) {
    event.preventDefault()
  }
  // ... 处理触摸
}
</script>
```

## 理解 .passive

```javascript
// .passive 告诉浏览器：
// "我承诺不会调用 preventDefault()"

// 这允许浏览器：
// 1. 立即开始滚动而无需等待 JS
// 2. 提高滚动性能，特别是在移动设备上
// 3. 减少卡顿和抖动

// 等效于：
element.addEventListener('scroll', handler, { passive: true })
```

## 何时使用 .passive

```html
<!-- .passive 的良好用例 -->

<!-- 滚动跟踪分析 -->
<div @scroll.passive="trackScrollPosition">

<!-- 触摸手势检测（不需要阻止） -->
<div @touchmove.passive="detectGesture">

<!-- 滚轮事件监控 -->
<div @wheel.passive="monitorWheel">
```

## 何时使用 .prevent（不使用 .passive）

```html
<!-- .prevent 的良好用例 -->

<!-- 表单提交 -->
<form @submit.prevent="handleSubmit">

<!-- 带有自定义导航的链接点击 -->
<a @click.prevent="navigate">

<!-- 阻止上下文菜单 -->
<div @contextmenu.prevent="showCustomMenu">
```

## 浏览器警告

当你组合 `.passive` 和 `.prevent` 时，浏览器控制台显示：
```
[Intervention] 由于目标被视为被动，
无法在被动事件侦听器内阻止默认行为。
```

## 参考
- [Vue.js 事件处理 - 事件修饰符](https://vuejs.org/guide/essentials/event-handling.html#event-modifiers)
- [MDN - 使用被动侦听器提高滚动性能](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#improving_scrolling_performance_with_passive_listeners)
