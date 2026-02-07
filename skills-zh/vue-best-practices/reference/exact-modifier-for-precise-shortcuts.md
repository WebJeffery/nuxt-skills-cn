---
title: 使用 .exact 修饰符进行精确的键盘/鼠标快捷键
impact: MEDIUM
impactDescription: 没有 .exact 时，即使按下其他修饰键，快捷键也会触发，导致意外行为
type: best-practice
tags: [vue3, events, keyboard, modifiers, shortcuts, accessibility]
---

# 使用 .exact 修饰符进行精确的键盘/鼠标快捷键

**影响: 中等** - 默认情况下，Vue 的修饰键处理程序（`.ctrl`、`.alt`、`.shift`、`.meta`）即使按下其他修饰键也会触发。使用 `.exact` 来要求只按下指定的修饰键，防止意外触发快捷键。

## 任务清单

- [ ] 当您需要精确的修饰键组合时使用 `.exact`
- [ ] 没有 `.exact`: `@click.ctrl` 在 Ctrl+Click 和 Ctrl+Shift+Click 时都会触发
- [ ] 使用 `.exact`: `@click.ctrl.exact` 只在 Ctrl+Click 时触发
- [ ] 使用 `@click.exact` 进行不带任何修饰键的普通点击

**不正确:**
```html
<!-- 错误: 即使有其他修饰键也会触发 -->
<template>
  <button @click.ctrl="copyItem">复制</button>
  <!-- 也会在 Ctrl+Shift+Click、Ctrl+Alt+Click 等时触发 -->

  <button @click.ctrl.shift="copyAll">复制全部</button>
  <!-- 用户期望 Ctrl+Shift，但也会在 Ctrl+Shift+Alt 时触发 -->
</template>
```

```html
<!-- 错误: 没有 .exact 的冲突快捷键 -->
<template>
  <div>
    <button @click.ctrl="copy">复制 (Ctrl+Click)</button>
    <button @click.ctrl.shift="copyAll">复制全部 (Ctrl+Shift+Click)</button>
    <!-- 当用户执行 Ctrl+Shift+Click 时两者都会触发！ -->
  </div>
</template>
```

**正确:**
```html
<!-- 正确: 使用 .exact 进行精确的修饰键匹配 -->
<template>
  <button @click.ctrl.exact="copyItem">复制 (仅 Ctrl)</button>
  <!-- 只在 Ctrl+Click 时触发，不在 Ctrl+Shift+Click 时触发 -->

  <button @click.ctrl.shift.exact="copyAll">复制全部 (仅 Ctrl+Shift)</button>
  <!-- 只在 Ctrl+Shift+Click 时触发，不在 Ctrl+Shift+Alt+Click 时触发 -->
</template>
```

```html
<!-- 正确: 不带任何修饰键的普通点击 -->
<template>
  <button @click.exact="selectItem">选择</button>
  <!-- 只在没有按下任何修饰键时触发 -->
  <!-- Ctrl+Click、Shift+Click 等不会触发此事件 -->
</template>
```

```html
<!-- 正确: 不冲突的快捷键 -->
<template>
  <div class="editor">
    <div
      @click.exact="selectItem"
      @click.ctrl.exact="addToSelection"
      @click.shift.exact="extendSelection"
      @click.ctrl.shift.exact="selectRange"
    >
      Click、Ctrl+Click、Shift+Click 或 Ctrl+Shift+Click
    </div>
  </div>
</template>
```

## Behavior Comparison

```javascript
// WITHOUT .exact
@click.ctrl="handler"
// Fires when: Ctrl+Click, Ctrl+Shift+Click, Ctrl+Alt+Click, Ctrl+Shift+Alt+Click
// Does NOT fire: Click (without Ctrl)

// WITH .exact
@click.ctrl.exact="handler"
// Fires when: ONLY Ctrl+Click
// Does NOT fire: Ctrl+Shift+Click, Ctrl+Alt+Click, Click

// ONLY .exact (no other modifiers)
@click.exact="handler"
// Fires when: Plain click with NO modifiers
// Does NOT fire: Ctrl+Click, Shift+Click, Alt+Click
```

## Practical Example: File Browser Selection

```vue
<template>
  <ul class="file-list">
    <li
      v-for="file in files"
      :key="file.id"
      @click.exact="selectSingle(file)"
      @click.ctrl.exact="toggleSelection(file)"
      @click.shift.exact="selectRange(file)"
      @click.ctrl.shift.exact="addRangeToSelection(file)"
      :class="{ selected: isSelected(file) }"
    >
      {{ file.name }}
    </li>
  </ul>
</template>

<script setup>
// Each click type has distinct, non-overlapping behavior
function selectSingle(file) {
  // Clear selection and select only this file
}

function toggleSelection(file) {
  // Add or remove this file from current selection
}

function selectRange(file) {
  // Select all files from last selected to this one
}

function addRangeToSelection(file) {
  // Add range to existing selection
}
</script>
```

## Keyboard Shortcuts with .exact

```html
<template>
  <div
    tabindex="0"
    @keydown.ctrl.s.exact.prevent="save"
    @keydown.ctrl.shift.s.exact.prevent="saveAs"
    @keydown.ctrl.z.exact.prevent="undo"
    @keydown.ctrl.shift.z.exact.prevent="redo"
  >
    <!-- Each shortcut is precisely defined -->
  </div>
</template>
```

## Reference
- [Vue.js Event Handling - .exact Modifier](https://vuejs.org/guide/essentials/event-handling.html#exact-modifier)
