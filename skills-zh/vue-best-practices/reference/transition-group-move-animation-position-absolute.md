---
title: TransitionGroup 移动动画需要离开项目上的绝对定位
impact: HIGH
impactDescription: 没有绝对定位，周围的项目会跳跃而不是平滑动画
type: gotcha
tags: [vue3, transition-group, animation, move-transition, css, list-animation]
---

# TransitionGroup 移动动画需要离开项目上的绝对定位

**影响: HIGH** - 当使用 `<TransitionGroup>` 从列表中添加或删除项目时，周围的项目会立即"跳跃"到新位置，而不是平滑动画。这会产生刺眼的用户体验，是列表动画中最常见的错误之一。

修复方法是在 `leave-active` 类上设置 `position: absolute`，以便离开的项目被移出布局流，允许其他项目平滑动画到新位置。

## 任务清单

- [ ] 添加 `.list-move` 类（或 `.[name]-move`）以实现平滑重新定位
- [ ] 在 `.list-leave-active` 类上设置 `position: absolute`
- [ ] 如果需要，确保父容器具有 `position: relative`
- [ ] 通过快速添加/删除操作测试以验证平滑动画

**不正确 - 项目跳跃而不是移动:**
```vue
<template>
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </TransitionGroup>
</template>

<style>
/* 不完整: 缺少 move 类和 position absolute */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>
```

**正确 - 平滑移动过渡:**
```vue
<template>
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </TransitionGroup>
</template>

<style>
/* 正确: 用于平滑动画的完整类集 */

/* 将过渡应用于移动元素 */
.list-move,
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* 关键: 将离开项目移出布局流 */
.list-leave-active {
  position: absolute;
}
</style>
```

## 为什么这样有效

Vue 内部使用的 FLIP 动画技术需要计算元素位置。当项目离开时:

1. 没有 `position: absolute`: 离开的项目仍然在 DOM 中占据空间
2. 其他项目无法移动，直到离开的项目完全被移除
3. 结果: 项目在离开动画完成后跳跃到新位置

使用 `position: absolute`:

1. 离开的项目立即从正常布局流中移除
2. 其他项目可以开始移动到空出的空间
3. 结果: 离开动画和移动动画同时发生

## 可视化图表

```
没有 position: absolute:
[A] [B] [C] [D]  <- 删除 B
[A]     [C] [D]  <- B 淡出，C/D 等待
[A] [C] [D]      <- B 消失，C/D 瞬间跳跃

有 position: absolute:
[A] [B] [C] [D]  <- 删除 B
[A][B][C] [D]    <- B 淡出（绝对定位），C/D 向左滑动
[A] [C] [D]      <- 平滑完成
```

## 其他考虑

**容器宽度:** 使用 `position: absolute` 时，项目可能需要显式宽度:

```css
.list-leave-active {
  position: absolute;
  width: 100%; /* 或特定宽度以在离开期间保持布局 */
}
```

**堆叠上下文:** 带有 `position: absolute` 的离开项目可能堆叠在其他元素之上:

```css
.list-leave-active {
  position: absolute;
  z-index: -1; /* 可选: 放在其他项目后面 */
}
```

## 参考
- [Vue.js TransitionGroup 移动过渡](https://vuejs.org/guide/built-ins/transition-group.html#move-transitions)
- [FLIP 动画技术](https://aerotwist.com/blog/flip-your-animations/)
