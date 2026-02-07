---
title: 鼠标按钮修饰符表示意图，而不是物理按钮
impact: LOW
impactDescription: 鼠标修饰符 .left/.right/.middle 可能不匹配惯用鼠标或其他输入设备上的物理按钮
type: gotcha
tags: [vue3, events, mouse, accessibility, modifiers]
---

# 鼠标按钮修饰符表示意图，而不是物理按钮

**影响: 低** - Vue 的鼠标按钮修饰符（`.left`、`.right`、`.middle`）是基于典型的惯用鼠标布局命名的，但它们实际上表示"主要"、"辅助"和"次要"指针设备触发器。这意味着它们可能不对应惯用鼠标、触控板或其他输入设备上的物理按钮位置。

## 任务清单

- [ ] 理解 `.left` 意味着"主要/主"操作，而不是物理左键
- [ ] 理解 `.right` 意味着"辅助"操作（通常是上下文菜单）
- [ ] 依赖特定鼠标按钮时考虑可访问性
- [ ] 不要假设用户有传统的惯用鼠标

**可能令人困惑：**
```html
<template>
  <!-- 文档说"仅左键点击"但是... -->
  <div @click.left="handlePrimaryAction">
    <!-- 在惯用鼠标上：在物理右键上触发 -->
    <!-- 在触控板上：在单指点击上触发 -->
    <!-- 在触摸屏上：在点击上触发 -->
  </div>
</template>
```

**清晰理解：**
```html
<template>
  <!-- 将其视为"主要操作" -->
  <div @click.left="handlePrimaryAction">
    主要操作（主按钮）
  </div>

  <!-- 将其视为"辅助/上下文操作" -->
  <div @click.right="handleSecondaryAction">
    辅助操作（上下文菜单按钮）
  </div>

  <!-- 将其视为"次要操作" -->
  <div @click.middle="handleAuxiliaryAction">
    次要操作（滚轮点击）
  </div>
</template>
```

## 修饰符的实际含义

```javascript
// Vue 修饰符 → MouseEvent.button 值 → 实际含义

// .left    → button === 0 → "主按钮"（主要操作）
// .right   → button === 2 → "辅助按钮"（上下文菜单）
// .middle  → button === 1 → "次要按钮"（中键点击）

// 浏览器处理以下情况的重新映射：
// - 惯用鼠标设置
// - 触控板手势
// - 触摸设备
// - 手写笔/笔输入
```

## 设备行为

```html
<!-- 不同设备如何触发这些修饰符 -->

<!-- 传统的惯用鼠标 -->
<!-- .left = 物理左键 -->
<!-- .right = 物理右键 -->
<!-- .middle = 滚轮按下 -->

<!-- 惯用鼠标（在操作系统设置中交换） -->
<!-- .left = 物理右键（由操作系统重新映射） -->
<!-- .right = 物理左键（由操作系统重新映射） -->
<!-- .middle = 滚轮按下 -->

<!-- 触控板 -->
<!-- .left = 单指点击/点击 -->
<!-- .right = 双指点击/点击（或角落点击） -->
<!-- .middle = 三指点击（如果配置） -->

<!-- 触摸屏 -->
<!-- .left = 点击 -->
<!-- .right = 长按（上下文菜单） -->
<!-- .middle = 通常不可用 -->
```

## 最佳实践：注释中的语义命名

```html
<template>
  <!-- 使用注释阐明意图 -->

  <!-- 主要操作（选择项目） -->
  <div @click.left="selectItem">

  <!-- 上下文菜单操作 -->
  <div @click.right.prevent="showContextMenu">

  <!-- 在新标签页中打开（次要/中键点击约定） -->
  <a @click.middle="openInNewTab" href="...">
</template>
```

## 可访问性考虑

```html
<template>
  <!-- 不要要求特定鼠标按钮进行基本操作 -->

  <!-- 更好：提供键盘替代方案 -->
  <div
    @click.left="select"
    @click.right.prevent="showMenu"
    @keydown.enter="select"
    @keydown.space="select"
    @contextmenu.prevent="showMenu"
    tabindex="0"
    role="button"
  >
    可访问的交互元素
  </div>
</template>
```

## 参考
- [Vue.js 事件处理 - 鼠标按钮修饰符](https://vuejs.org/guide/essentials/event-handling.html#mouse-button-modifiers)
- [MDN - MouseEvent.button](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/button)
