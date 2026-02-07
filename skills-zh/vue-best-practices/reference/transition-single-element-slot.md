---
title: 过渡仅支持单个元素或组件作为插槽内容
impact: HIGH
impactDescription: 在 Transition 中使用多个元素会导致过渡静默失败或产生意外结果
type: gotcha
tags: [vue3, transition, animation, slot, single-element]
---

# 过渡仅支持单个元素或组件作为插槽内容

**影响：高** - `<Transition>` 组件只能包装单个元素或组件。如果您传递多个元素或具有多个根元素的组件，过渡将无法正常工作，并且可能静默失败而没有任何错误消息。

这是使用 Vue 过渡时最常见的错误之一，特别是对于来自其他框架的开发人员或在重构现有代码时。

## 任务清单

- [ ] 确保在 `<Transition>` 内只有一个元素或组件
- [ ] 如果包装组件，请验证组件只有一个根元素
- [ ] 使用 `v-if`/`v-else` 时，元素必须互斥（一次只能有一个可见）
- [ ] 如果需要一起过渡多个元素，请使用包装器 `<div>`

**有问题的代码：**
```vue
<template>
  <!-- 错误：Transition 内有多个元素 -->
  <Transition name="fade">
    <h1>标题</h1>
    <p>内容</p>
  </Transition>
</template>
```

```vue
<template>
  <!-- 错误：具有多个根元素的组件 -->
  <Transition name="fade">
    <MyMultiRootComponent v-if="show" />
  </Transition>
</template>

<!-- MyMultiRootComponent.vue -->
<template>
  <header>页眉</header>
  <main>主要内容</main>
  <footer>页脚</footer>
</template>
```

**正确的代码：**
```vue
<template>
  <!-- 好：单个包装器元素 -->
  <Transition name="fade">
    <div v-if="show">
      <h1>标题</h1>
      <p>内容</p>
    </div>
  </Transition>
</template>
```

```vue
<template>
  <!-- 好：v-if/v-else 与互斥元素 -->
  <Transition name="fade" mode="out-in">
    <button v-if="isEditing" @click="save">保存</button>
    <button v-else @click="edit">编辑</button>
  </Transition>
</template>
```

```vue
<template>
  <!-- 好：具有单个根的组件 -->
  <Transition name="fade">
    <MyComponent v-if="show" />
  </Transition>
</template>

<!-- MyComponent.vue -->
<template>
  <div class="my-component">
    <header>页眉</header>
    <main>主要内容</main>
    <footer>页脚</footer>
  </div>
</template>
```

## 带有过渡的动态组件

```vue
<template>
  <!-- 好：动态组件作为单个元素工作 -->
  <Transition name="fade" mode="out-in">
    <component :is="currentView" />
  </Transition>
</template>

<script setup>
import { shallowRef } from 'vue'
import Home from './Home.vue'
import About from './About.vue'

const currentView = shallowRef(Home)
</script>
```

## 此问题的常见症状

1. 没有过渡动画发生
2. 元素立即出现/消失
3. 不一致的动画行为
4. Vue devtools 没有显示过渡状态变化

## 参考
- [Vue.js Transition Documentation](https://vuejs.org/guide/built-ins/transition.html)
- [Vue.js Dynamic Components](https://vuejs.org/guide/essentials/component-basics.html#dynamic-components)
