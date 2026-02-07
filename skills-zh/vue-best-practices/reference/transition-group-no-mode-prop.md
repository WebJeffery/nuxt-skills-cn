---
title: TransitionGroup 不支持过渡模式
impact: LOW
impactDescription: 期望从 Transition 获取 mode 属性的开发者会发现它不起作用
type: gotcha
tags: [vue3, transition-group, animation, mode, in-out, out-in]
---

# TransitionGroup 不支持过渡模式

**影响: LOW** - 与 `<Transition>` 不同，`<TransitionGroup>` 组件不支持 `mode` 属性（`in-out` 或 `out-in`）。这是因为 `<TransitionGroup>` 处理可以同时进入和离开的多个元素，而不是在互斥元素之间交替。

如果需要顺序的进入/离开行为，必须使用 JavaScript 钩子手动实现，或者重新考虑动画方法。

## 任务清单

- [ ] 不要在 `<TransitionGroup>` 上使用 `mode` 属性（它将被忽略）
- [ ] 对于顺序动画，使用带有延迟的 JavaScript 钩子
- [ ] 对于单个元素交替，改用 `<Transition>`
- [ ] 考虑使用交错动画作为基于模式的排序的替代方案

**不正确 - mode 属性被忽略:**
```vue
<template>
  <!-- mode="out-in" 对 TransitionGroup 没有效果 -->
  <TransitionGroup name="list" tag="ul" mode="out-in">
    <li v-for="item in items" :key="item.id">{{ item }}</li>
  </TransitionGroup>
</template>
```

## 何时使用 Transition vs TransitionGroup

| 场景 | 组件 |
|----------|-----------|
| 单个元素切换 (v-if) | `<Transition>` |
| 在两个组件之间交替 | 带有 `mode` 的 `<Transition>` |
| 项目列表 (v-for) | `<TransitionGroup>` |
| 多个同时动画 | `<TransitionGroup>` |

**单个元素 - 使用带 mode 的 Transition:**
```vue
<template>
  <!-- Transition 支持单个/交替元素的 mode -->
  <Transition name="fade" mode="out-in">
    <component :is="currentComponent" :key="currentKey" />
  </Transition>
</template>
```

**元素列表 - 使用 TransitionGroup:**
```vue
<template>
  <!-- TransitionGroup 用于列表，不支持 mode -->
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">{{ item }}</li>
  </TransitionGroup>
</template>
```

## 模拟顺序行为

如果需要项目在新项目进入之前离开，使用 JavaScript 钩子:

```vue
<template>
  <TransitionGroup
    name="list"
    tag="ul"
    :css="false"
    @before-enter="onBeforeEnter"
    @enter="onEnter"
    @leave="onLeave"
  >
    <li v-for="item in items" :key="item.id">{{ item }}</li>
  </TransitionGroup>
</template>

<script setup>
import { ref } from 'vue'

const isLeaving = ref(false)
const enterQueue = []

function onBeforeEnter(el) {
  el.style.opacity = 0
  if (isLeaving.value) {
    // 在离开发生时排队进入
    enterQueue.push(el)
  }
}

function onEnter(el, done) {
  if (isLeaving.value) {
    // 等待离开完成
    return
  }

  // 动画进入
  el.offsetHeight // 强制重排
  el.style.transition = 'opacity 0.3s'
  el.style.opacity = 1

  setTimeout(done, 300)
}

function onLeave(el, done) {
  isLeaving.value = true

  el.style.transition = 'opacity 0.3s'
  el.style.opacity = 0

  setTimeout(() => {
    done()
    isLeaving.value = false

    // 处理排队的进入
    enterQueue.forEach(queuedEl => {
      queuedEl.style.transition = 'opacity 0.3s'
      queuedEl.style.opacity = 1
    })
    enterQueue.length = 0
  }, 300)
}
</script>
```

## 交错动画替代方案

不要使用基于模式的顺序行为，考虑使用交错动画以获得精致的效果:

```vue
<template>
  <TransitionGroup
    name="list"
    tag="ul"
    @before-enter="onBeforeEnter"
    @enter="onEnter"
  >
    <li
      v-for="(item, index) in items"
      :key="item.id"
      :data-index="index"
    >
      {{ item }}
    </li>
  </TransitionGroup>
</template>

<script setup>
function onBeforeEnter(el) {
  el.style.opacity = 0
  el.style.transform = 'translateY(20px)'
}

function onEnter(el, done) {
  const delay = el.dataset.index * 100

  setTimeout(() => {
    el.style.transition = 'all 0.3s ease'
    el.style.opacity = 1
    el.style.transform = 'translateY(0)'

    setTimeout(done, 300)
  }, delay)
}
</script>
```

## 参考
- [Vue.js TransitionGroup](https://vuejs.org/guide/built-ins/transition-group.html)
- [Vue.js Transition Modes](https://vuejs.org/guide/built-ins/transition.html#transition-modes)
