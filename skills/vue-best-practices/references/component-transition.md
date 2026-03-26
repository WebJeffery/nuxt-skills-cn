---
title: Transition 组件最佳实践
impact: MEDIUM
impactDescription: Transition 为单个元素或组件设置动画;不正确的结构或键会阻止动画
type: best-practice
tags: [vue3, transition, animation, performance, keys]
---

# Transition 组件最佳实践

**影响: MEDIUM** - `<Transition>` 为单个元素或组件的进入/离开设置动画。它非常适合切换 UI 状态、交换视图或一次为一个组件设置动画。

## 任务列表

- 在 `<Transition>` 内包装单个元素或组件
- 在相同元素类型之间切换时提供 `key`
- 当您需要顺序交换时使用 `mode="out-in"`
- 偏好 `transform` 和 `opacity` 以获得平滑动画

## 对单个根元素使用 Transition

`<Transition>` 仅支持一个直接子级。将多个节点包装在单个元素或组件中。

**错误:**
```vue
<template>
  <Transition name="fade">
    <h3>标题</h3>
    <p>描述</p>
  </Transition>
</template>
```

**正确:**
```vue
<template>
  <Transition name="fade">
    <div>
      <h3>标题</h3>
      <p>描述</p>
    </div>
  </Transition>
</template>
```

## 在相同元素类型之间强制过渡

当标签类型不更改时,Vue 重用相同的 DOM 元素。添加 `key` 以便 Vue 将其视为新元素并触发进入/离开。

**错误:**
```vue
<template>
  <Transition name="fade">
    <p v-if="isActive">活动</p>
    <p v-else>不活动</p>
  </Transition>
</template>
```

**正确:**
```vue
<template>
  <Transition name="fade" mode="out-in">
    <p v-if="isActive" key="active">活动</p>
    <p v-else key="inactive">不活动</p>
  </Transition>
</template>
```

## 使用 `mode` 避免交换期间重叠

当交换组件或视图时,使用 `mode="out-in"` 以防止两者同时可见。

**错误:**
```vue
<template>
  <Transition name="fade">
    <component :is="currentView" />
  </Transition>
</template>
```

**正确:**
```vue
<template>
  <Transition name="fade" mode="out-in">
    <component :is="currentView" :key="currentView" />
  </Transition>
</template>
```

## 为性能动画 `transform` 和 `opacity`

避免触发布局的属性,如 `height`、`margin` 或 `top`。使用 `transform` 和 `opacity` 进行平滑、GPU 友好的过渡。

**错误:**
```css
.slide-enter-active,
.slide-leave-active {
  transition: height 0.3s ease;
}

.slide-enter-from,
.slide-leave-to {
  height: 0;
}
```

**正确:**
```css
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.slide-enter-from {
  transform: translateX(-12px);
  opacity: 0;
}

.slide-leave-to {
  transform: translateX(12px);
  opacity: 0;
}
```
