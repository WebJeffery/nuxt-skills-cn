---
name: advanced-patterns
description: Vue 3 内置组件(Transition、Teleport、Suspense、KeepAlive)和高级指令
---

# 内置组件 & 指令

## Transition

为单个元素或组件的进入/离开设置动画。

```vue
<template>
  <Transition name="fade">
    <div v-if="show">内容</div>
  </Transition>
</template>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>
```

### CSS 类

| 类 | 时机 |
|-------|------|
| `{name}-enter-from` | 进入的开始状态 |
| `{name}-enter-active` | 进入的活动状态(在此添加过渡) |
| `{name}-enter-to` | 进入的结束状态 |
| `{name}-leave-from` | 离开的开始状态 |
| `{name}-leave-active` | 离开的活动状态 |
| `{name}-leave-to` | 离开的结束状态 |

### 过渡模式

```vue
<!-- 等待离开完成后再进入 -->
<Transition name="fade" mode="out-in">
  <component :is="currentView" />
</Transition>
```

### JavaScript 钩子

```vue
<Transition
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @leave="onLeave"
  :css="false"
>
  <div v-if="show">内容</div>
</Transition>

<script setup lang="ts">
function onEnter(el: Element, done: () => void) {
  // 使用 JS 库进行动画
  gsap.to(el, { opacity: 1, onComplete: done })
}
</script>
```

### 初始渲染时出现

```vue
<Transition appear name="fade">
  <div>挂载时显示动画</div>
</Transition>
```

## TransitionGroup

为列表项设置动画。每个子元素必须有唯一的 `key`。

```vue
<template>
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </TransitionGroup>
</template>

<style>
.list-enter-active, .list-leave-active {
  transition: all 0.3s ease;
}
.list-enter-from, .list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
/* 重新排序的移动动画 */
.list-move {
  transition: transform 0.3s ease;
}
</style>
```

## Teleport

将内容渲染到不同的 DOM 位置。

```vue
<template>
  <button @click="open = true">打开模态框</button>
  
  <Teleport to="body">
    <div v-if="open" class="modal">
      模态框内容渲染在 body 中
    </div>
  </Teleport>
</template>
```

### Props

```vue
<!-- CSS 选择器 -->
<Teleport to="#modal-container">

<!-- DOM 元素 -->
<Teleport :to="targetElement">

<!-- 有条件地禁用 teleport -->
<Teleport to="body" :disabled="isMobile">

<!-- 延迟到目标存在(Vue 3.5+) -->
<Teleport defer to="#late-rendered-target">
```

## Suspense

使用加载状态处理异步依赖。**实验性功能。**

```vue
<template>
  <Suspense>
    <template #default>
      <AsyncComponent />
    </template>
    <template #fallback>
      <div>加载中...</div>
    </template>
  </Suspense>
</template>
```

### 异步依赖

Suspense 等待:
- 具有 `async setup()` 的组件
- 在 `<script setup>` 中使用顶层 `await` 的组件
- 使用 `defineAsyncComponent` 创建的异步组件

```vue
<!-- AsyncComponent.vue -->
<script setup lang="ts">
const data = await fetch('/api/data').then(r => r.json())
</script>
```

### 事件

```vue
<Suspense
  @pending="onPending"
  @resolve="onResolve"
  @fallback="onFallback"
>
  ...
</Suspense>
```

## KeepAlive

在切换时缓存组件实例。

```vue
<template>
  <KeepAlive>
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

### 包含/排除

```vue
<!-- 按名称(字符串或正则) -->
<KeepAlive include="ComponentA,ComponentB">
<KeepAlive :include="/^Tab/">
<KeepAlive :include="['TabA', 'TabB']">

<!-- 排除 -->
<KeepAlive exclude="ModalComponent">

<!-- 最大缓存实例数 -->
<KeepAlive :max="10">
```

### 生命周期钩子

```ts
import { onActivated, onDeactivated } from 'vue'

onActivated(() => {
  // 当组件从缓存插入时调用
  fetchLatestData()
})

onDeactivated(() => {
  // 当组件移除到缓存时调用
  pauseTimers()
})
```

## v-memo

当依赖项未更改时跳过重新渲染。用于性能优化。

```vue
<template>
  <div v-for="item in list" :key="item.id" v-memo="[item.selected]">
    <!-- 仅在 item.selected 更改时重新渲染 -->
    <ExpensiveComponent :item="item" />
  </div>
</template>
```

为空时等同于 `v-once`:
```vue
<div v-memo="[]">永不更新</div>
```

## v-once

渲染一次,跳过所有未来的更新。

```vue
<span v-once>静态: {{ neverChanges }}</span>
```

## 自定义指令

创建可重用的 DOM 操作。

```ts
// 指令定义
const vFocus: Directive<HTMLElement> = {
  mounted: (el) => el.focus()
}

// 完整钩子
const vColor: Directive<HTMLElement, string> = {
  created(el, binding, vnode, prevVnode) {},
  beforeMount(el, binding) {},
  mounted(el, binding) {
    el.style.color = binding.value
  },
  beforeUpdate(el, binding) {},
  updated(el, binding) {
    el.style.color = binding.value
  },
  beforeUnmount(el, binding) {},
  unmounted(el, binding) {}
}
```

### 指令参数 & 修饰符

```vue
<div v-color:background.bold="'red'">

<script setup lang="ts">
const vColor: Directive<HTMLElement, string> = {
  mounted(el, binding) {
    // binding.arg = 'background'
    // binding.modifiers = { bold: true }
    // binding.value = 'red'
    el.style[binding.arg || 'color'] = binding.value
    if (binding.modifiers.bold) {
      el.style.fontWeight = 'bold'
    }
  }
}
</script>
```

### 全局注册

```ts
// main.ts
app.directive('focus', {
  mounted: (el) => el.focus()
})
```

<!--
Source references:
- https://vuejs.org/api/built-in-components.html
- https://vuejs.org/guide/built-ins/transition.html
- https://vuejs.org/guide/built-ins/teleport.html
- https://vuejs.org/guide/built-ins/suspense.html
- https://vuejs.org/guide/built-ins/keep-alive.html
- https://vuejs.org/api/built-in-directives.html
- https://vuejs.org/guide/reusability/custom-directives.html
-->
