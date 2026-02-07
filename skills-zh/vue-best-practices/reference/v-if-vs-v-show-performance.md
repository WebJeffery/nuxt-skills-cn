---
title: 根据切换频率选择 v-if 或 v-show
impact: MEDIUM
impactDescription: 错误的选择会导致不必要的 DOM 操作或浪费初始渲染成本
type: capability
tags: [vue3, conditional-rendering, v-if, v-show, performance]
---

# 根据切换频率选择 v-if 或 v-show

**影响：中** - 为你的用例使用错误的指令会导致性能不佳。`v-if` 具有更高的切换成本（销毁/重新创建元素），而 `v-show` 具有更高的初始渲染成本（始终在 DOM 中）。错误选择可能导致不必要的 DOM 操作或在隐藏元素上浪费内存。

`v-if` 是"真正的"条件渲染，它正确地销毁和重新创建事件监听器和子组件。`v-show` 只是切换 CSS `display` 属性，将所有内容保留在 DOM 中。

## 任务清单

- [ ] 对频繁切换的元素使用 `v-show`（模态框、下拉菜单、面板）
- [ ] 对在运行时很少更改的条件使用 `v-if`（身份验证状态、功能标志）
- [ ] 当初始条件可能为 false 时使用 `v-if`（延迟渲染节省成本）
- [ ] 当子组件具有昂贵的设置/拆卸时使用 `v-if`
- [ ] 考虑使用 v-show 在 DOM 中保留隐藏元素的内存成本

**错误：**
```html
<!-- 错误：频繁切换使用 v-if 导致昂贵的 DOM 操作 -->
<template>
  <button @click="showPanel = !showPanel">Toggle</button>

  <!-- 用户经常点击这个 - v-if 每次都销毁/重新创建 -->
  <div v-if="showPanel" class="settings-panel">
    <ComplexForm />
  </div>
</template>
```

```html
<!-- 错误：很少为真的条件使用 v-show 浪费初始渲染 -->
<template>
  <!-- 管理面板仅对管理员用户显示（罕见），但 v-show 为所有人渲染它 -->
  <AdminPanel v-show="user.isAdmin" />
</template>
```

**正确：**
```html
<!-- 正确：频繁切换使用 v-show - 便宜的 CSS 切换 -->
<template>
  <button @click="showPanel = !showPanel">Toggle</button>

  <!-- v-show 只是切换 display: none，没有 DOM 销毁 -->
  <div v-show="showPanel" class="settings-panel">
    <ComplexForm />
  </div>
</template>
```

```html
<!-- 正确：罕见条件使用 v-if - 延迟渲染 -->
<template>
  <!-- 仅为管理员渲染，非管理员不支付渲染成本 -->
  <AdminPanel v-if="user.isAdmin" />
</template>
```

```html
<!-- 正确：身份验证门控内容使用 v-if -->
<template>
  <!-- v-if 在这里很理想 - 身份验证状态很少更改 -->
  <div v-if="isAuthenticated">
    <UserDashboard />
  </div>
  <div v-else>
    <LoginPrompt />
  </div>
</template>
```

## 决策指南

| 场景 | 建议 | 原因 |
|----------|----------------|--------|
| 频繁切换（选项卡、手风琴、下拉菜单） | `v-show` | 便宜的 CSS 切换 |
| 很少切换（身份验证、功能标志） | `v-if` | 延迟渲染，干净的 DOM |
| 最初为 false，可能永远不会为 true | `v-if` | 从不支付渲染成本 |
| 具有状态的复杂子组件 | `v-if` | 在切换时正确重置状态 |
| 需要在切换之间保留组件状态 | `v-show` | 组件保持活动状态 |

## 主要区别

```javascript
// v-if 行为：
// - 条件为 false：元素不在 DOM 中，子组件未创建
// - 条件为 true：元素被添加，子组件被创建，mounted 钩子触发
// - 条件再次为 false：元素被移除，子组件被销毁，unmounted 钩子触发

// v-show 行为：
// - 条件为 false：元素在 DOM 中带有 display: none，子组件存在
// - 条件为 true：display: none 被移除
// - 组件生命周期钩子仅触发一次（在初始挂载时）
```

## 参考
- [Vue.js 条件渲染 - v-if vs v-show](https://vuejs.org/guide/essentials/conditional.html#v-if-vs-v-show)
