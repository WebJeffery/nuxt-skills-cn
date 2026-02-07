---
title: 为嵌套过渡指定显式持续时间
impact: MEDIUM
impactDescription: 当 Vue 仅检测根元素的过渡结束时，具有不同时序的嵌套过渡可能会过早结束
type: gotcha
tags: [vue3, transition, animation, duration, nested, timing]
---

# 为嵌套过渡指定显式持续时间

**影响：中等** - 当转换包含具有不同动画时序的嵌套子元素时，Vue 默认只监听**根**过渡元素上的第一个 `transitionend` 或 `animationend` 事件。这意味着如果内部元素具有更长或延迟的动画，它们可能会在根元素的过渡完成时被切断。

## 任务清单

- [ ] 确定您的过渡是否包含具有不同动画持续时间的嵌套元素
- [ ] 使用 `:duration` 属性指定 Vue 应该等待的总时间
- [ ] 如果进入和离开持续时间不同，考虑使用单独的持续时间和离开持续时间
- [ ] 测试动画以确保嵌套元素完全完成

**有问题的代码：**
```vue
<template>
  <!-- 错误：内部元素具有被切断的较长动画 -->
  <Transition name="nested">
    <div v-if="show" class="outer">
      <div class="inner">你好</div>
    </div>
  </Transition>
</template>

<style>
.nested-enter-active .outer,
.nested-leave-active .outer {
  transition: opacity 0.3s ease;
}

.nested-enter-active .inner,
.nested-leave-active .inner {
  /* 这个 0.5s 动画在外部完成时在 0.3s 处被切断！ */
  transition: transform 0.5s ease 0.2s; /* 0.2s 延迟 + 0.5s = 0.7s 总计 */
}

.nested-enter-from .outer,
.nested-leave-to .outer {
  opacity: 0;
}

.nested-enter-from .inner,
.nested-leave-to .inner {
  transform: translateX(-30px);
}
</style>
```

**正确的代码：**
```vue
<template>
  <!-- 好：显式持续时间确保所有嵌套动画完成 -->
  <Transition name="nested" :duration="700">
    <div v-if="show" class="outer">
      <div class="inner">你好</div>
    </div>
  </Transition>
</template>

<style>
.nested-enter-active .outer,
.nested-leave-active .outer {
  transition: opacity 0.3s ease;
}

.nested-enter-active .inner,
.nested-leave-active .inner {
  /* 现在这个动画完全完成 */
  transition: transform 0.5s ease 0.2s;
}

.nested-enter-from .outer,
.nested-leave-to .outer {
  opacity: 0;
}

.nested-enter-from .inner,
.nested-leave-to .inner {
  transform: translateX(-30px);
}
</style>
```

## 不同的进入和离开持续时间

```vue
<template>
  <!-- 好：进入和离开的单独持续时间 -->
  <Transition
    name="complex"
    :duration="{ enter: 500, leave: 800 }"
  >
    <div v-if="show" class="container">
      <h1 class="title">标题</h1>
      <p class="content">带有交错动画的内容</p>
    </div>
  </Transition>
</template>

<style>
/* 进入：先标题，然后内容 */
.complex-enter-active .title {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.complex-enter-active .content {
  transition: opacity 0.3s ease 0.2s, transform 0.3s ease 0.2s;
}

/* 离开：先内容，然后标题（相反顺序） */
.complex-leave-active .content {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.complex-leave-active .title {
  transition: opacity 0.5s ease 0.3s, transform 0.5s ease 0.3s;
}

.complex-enter-from .title,
.complex-enter-from .content,
.complex-leave-to .title,
.complex-leave-to .content {
  opacity: 0;
  transform: translateY(20px);
}
</style>
```

## 编排的交错动画

```vue
<template>
  <Transition name="stagger" :duration="800">
    <div v-if="show" class="card">
      <img class="card-image" src="..." />
      <h2 class="card-title">标题</h2>
      <p class="card-body">正文...</p>
      <button class="card-action">操作</button>
    </div>
  </Transition>
</template>

<style>
/* 交错进入：图像 -> 标题 -> 正文 -> 操作 */
.stagger-enter-active .card-image { transition: all 0.3s ease; }
.stagger-enter-active .card-title { transition: all 0.3s ease 0.1s; }
.stagger-enter-active .card-body { transition: all 0.3s ease 0.2s; }
.stagger-enter-active .card-action { transition: all 0.3s ease 0.3s; }
/* 总计：0.3s 延迟 + 0.3s 动画 = 0.6s，但为了安全使用 800ms */

.stagger-enter-from .card-image,
.stagger-enter-from .card-title,
.stagger-enter-from .card-body,
.stagger-enter-from .card-action {
  opacity: 0;
  transform: translateY(10px);
}
</style>
```

## 计算持续时间

使用此公式计算正确的持续时间：
```
duration = 所有嵌套元素的 max(delay + animation_duration)
```

示例：
- 元素 A：无延迟，300ms 持续时间 = 300ms 总计
- 元素 B：100ms 延迟，300ms 持续时间 = 400ms 总计
- 元素 C：200ms 延迟，500ms 持续时间 = 700ms 总计

**所需的 `:duration`**：700（或略高一些以获得安全边距）

## 参考
- [Vue.js Transition - Nested Transitions](https://vuejs.org/guide/built-ins/transition.html#nested-transitions-and-explicit-transition-durations)
