---
title: 避免在可重用过渡组件中使用作用域样式
impact: MEDIUM
impactDescription: 过渡包装器组件中的作用域样式不会应用于插槽内容，破坏过渡动画
type: gotcha
tags: [vue3, transition, scoped-css, slot, reusable-component]
---

# 避免在可重用过渡组件中使用作用域样式

**影响：中等** - 创建可重用过渡包装器组件时，使用 `<style scoped>` 将阻止过渡 CSS 类应用于插槽内容。作用域样式仅影响组件模板中的元素，不影响通过插槽传递的内容。您的过渡动画将静默失败。

## 任务清单

- [ ] 在可重用过渡组件中，使用不带 `scoped` 的 `<style>`
- [ ] 或者，使用唯一的类名前缀以避免全局冲突
- [ ] 或者，对过渡类使用带有 `:global()` 的 CSS 模块
- [ ] 测试组件在不同上下文中使用时过渡是否工作

**有问题的代码：**
```vue
<!-- MyFadeTransition.vue -->
<template>
  <Transition name="my-fade">
    <slot />
  </Transition>
</template>

<!-- 错误：作用域样式不会应用于插槽内容！ -->
<style scoped>
.my-fade-enter-active,
.my-fade-leave-active {
  transition: opacity 0.3s ease;
}

.my-fade-enter-from,
.my-fade-leave-to {
  opacity: 0;
}
</style>
```

```vue
<!-- 使用过渡的父组件 -->
<template>
  <MyFadeTransition>
    <div v-if="show">这不会动画！</div>
  </MyFadeTransition>
</template>

<!--
<div> 是插槽内容，所以 Vue 应用的 .my-fade-* 类
不会匹配作用域 CSS 选择器
-->
```

**正确的代码：**
```vue
<!-- MyFadeTransition.vue -->
<template>
  <Transition name="my-fade">
    <slot />
  </Transition>
</template>

<!-- 好：非作用域样式应用于任何元素 -->
<style>
.my-fade-enter-active,
.my-fade-leave-active {
  transition: opacity 0.3s ease;
}

.my-fade-enter-from,
.my-fade-leave-to {
  opacity: 0;
}
</style>
```

## 替代方案：使用唯一前缀类名

为了避免全局样式冲突，使用独特的前缀：

```vue
<!-- FadeTransition.vue -->
<template>
  <Transition name="v-fade-transition">
    <slot />
  </Transition>
</template>

<style>
/* 唯一前缀减少冲突风险 */
.v-fade-transition-enter-active,
.v-fade-transition-leave-active {
  transition: opacity 0.3s ease;
}

.v-fade-transition-enter-from,
.v-fade-transition-leave-to {
  opacity: 0;
}
</style>
```

## 替代方案：CSS 模块与 :global()

```vue
<!-- FadeTransition.vue -->
<template>
  <Transition name="fade">
    <slot />
  </Transition>
</template>

<style module>
/* 对过渡类使用 :global() */
:global(.fade-enter-active),
:global(.fade-leave-active) {
  transition: opacity 0.3s ease;
}

:global(.fade-enter-from),
:global(.fade-leave-to) {
  opacity: 0;
}
</style>
```

## 替代方案：自定义过渡类

使用自定义类属性应用作用域类：

```vue
<!-- FadeTransition.vue -->
<template>
  <Transition
    :enter-active-class="$style.enterActive"
    :leave-active-class="$style.leaveActive"
    :enter-from-class="$style.enterFrom"
    :leave-to-class="$style.leaveTo"
  >
    <slot />
  </Transition>
</template>

<style module>
.enterActive,
.leaveActive {
  transition: opacity 0.3s ease;
}

.enterFrom,
.leaveTo {
  opacity: 0;
}
</style>
```

## 完整的可重用过渡组件示例

```vue
<!-- transitions/SlideTransition.vue -->
<template>
  <Transition
    name="slide"
    :mode="mode"
    :appear="appear"
    @before-enter="$emit('before-enter', $event)"
    @enter="$emit('enter', $event)"
    @after-enter="$emit('after-enter', $event)"
    @before-leave="$emit('before-leave', $event)"
    @leave="$emit('leave', $event)"
    @after-leave="$emit('after-leave', $event)"
  >
    <slot />
  </Transition>
</template>

<script setup>
defineProps({
  mode: {
    type: String,
    default: 'out-in',
    validator: (v) => ['out-in', 'in-out', ''].includes(v)
  },
  appear: {
    type: Boolean,
    default: false
  }
})

defineEmits([
  'before-enter', 'enter', 'after-enter',
  'before-leave', 'leave', 'after-leave'
])
</script>

<!-- 非作用域，以便样式应用于插槽内容 -->
<style>
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.slide-enter-from {
  opacity: 0;
  transform: translateX(-20px);
}

.slide-leave-to {
  opacity: 0;
  transform: translateX(20px);
}
</style>
```

使用：
```vue
<template>
  <SlideTransition>
    <div v-if="show" class="content">
      这将正确动画！
    </div>
  </SlideTransition>
</template>
```

## 为什么会发生这种情况

Vue 的作用域样式通过向元素和选择器添加唯一的 data 属性（例如，`data-v-7ba5bd90`）来工作：

```css
/* 您编写的 */
.my-fade-enter-active { ... }

/* Vue 生成的（作用域） */
.my-fade-enter-active[data-v-7ba5bd90] { ... }
```

插槽内容来自父组件，并获得父组件的 data 属性，而不是过渡组件的属性。所以选择器永远不会匹配。

## 参考
- [Vue.js Reusable Transitions](https://vuejs.org/guide/built-ins/transition.html#reusable-transitions)
- [Vue.js Scoped CSS](https://vuejs.org/api/sfc-css-features.html#scoped-css)
