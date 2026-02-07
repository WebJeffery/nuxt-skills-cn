---
title: 在 TransitionGroup 中使用数据属性进行交错列表动画
impact: LOW
impactDescription: 创建精美的交错动画效果的最佳实践
type: best-practice
tags: [vue3, transition-group, animation, stagger, javascript-hooks, gsap]
---

# 在 TransitionGroup 中使用数据属性进行交错列表动画

**影响：低** - 当创建交错列表动画（项目按顺序延迟动画）时，使用数据属性将项目索引传递给 JavaScript 钩子。这可以实现优雅的级联效果，仅使用 CSS 很难实现。

当添加新项目或列表首次出现时，此模式对于进入动画特别有用。

## 任务清单

- [ ] 向列表项添加 data-index 属性
- [ ] 使用仅 JavaScript 动画时使用 `:css="false"`
- [ ] 在进入/离开钩子中基于 data-index 计算延迟
- [ ] 动画完成时调用 `done()` 回调
- [ ] 考虑使用动画库（GSAP）进行复杂动画

**基本模式：**
```vue
<template>
  <TransitionGroup
    tag="ul"
    :css="false"
    @before-enter="onBeforeEnter"
    @enter="onEnter"
    @leave="onLeave"
  >
    <li
      v-for="(item, index) in items"
      :key="item.id"
      :data-index="index"
    >
      {{ item.name }}
    </li>
  </TransitionGroup>
</template>

<script setup>
function onBeforeEnter(el) {
  el.style.opacity = 0
  el.style.transform = 'translateY(20px)'
}

function onEnter(el, done) {
  const delay = el.dataset.index * 150 // 每个项目之间 150ms

  setTimeout(() => {
    el.style.transition = 'all 0.4s ease-out'
    el.style.opacity = 1
    el.style.transform = 'translateY(0)'

    // 过渡完成后调用 done
    setTimeout(done, 400)
  }, delay)
}

function onLeave(el, done) {
  const delay = el.dataset.index * 100

  setTimeout(() => {
    el.style.transition = 'all 0.3s ease-in'
    el.style.opacity = 0
    el.style.transform = 'translateY(-20px)'

    setTimeout(done, 300)
  }, delay)
}
</script>
```

**使用 GSAP 动画库：**
```vue
<template>
  <TransitionGroup
    tag="ul"
    :css="false"
    @before-enter="onBeforeEnter"
    @enter="onEnter"
    @leave="onLeave"
  >
    <li
      v-for="(item, index) in computedList"
      :key="item.id"
      :data-index="index"
    >
      {{ item.msg }}
    </li>
  </TransitionGroup>
</template>

<script setup>
import gsap from 'gsap'

function onBeforeEnter(el) {
  el.style.opacity = 0
  el.style.height = 0
}

function onEnter(el, done) {
  gsap.to(el, {
    opacity: 1,
    height: '1.6em',
    delay: el.dataset.index * 0.15,
    onComplete: done
  })
}

function onLeave(el, done) {
  gsap.to(el, {
    opacity: 0,
    height: 0,
    delay: el.dataset.index * 0.15,
    onComplete: done
  })
}
</script>
```

## 理解 `:css="false"` 属性

设置后，Vue 跳过 CSS 过渡检测：

```vue
<!-- Vue 将查找 CSS 类并检测持续时间 -->
<TransitionGroup name="list">

<!-- Vue 跳过 CSS，仅使用 JavaScript 钩子 -->
<TransitionGroup :css="false">
```

在以下情况下使用 `:css="false"`：
- 您在 JavaScript 中处理所有动画
- 您想避免 CSS 类应用开销
- 您正在使用 JavaScript 动画库（GSAP、anime.js 等）

## 用于过滤列表的计算索引

过滤列表时，`v-for` 中的索引会更改。考虑使用计算属性：

```vue
<script setup>
import { ref, computed } from 'vue'

const items = ref([
  { id: 1, msg: 'Bruce Lee', show: true },
  { id: 2, msg: 'Jackie Chan', show: true },
  { id: 3, msg: 'Chuck Norris', show: true }
])

const query = ref('')

const computedList = computed(() => {
  return items.value.filter(item =>
    item.msg.toLowerCase().includes(query.value.toLowerCase())
  )
})
</script>
```

## 双向交错（中心向外动画）

对于更高级的效果，从中心计算延迟：

```javascript
function onEnter(el, done) {
  const totalItems = document.querySelectorAll('[data-index]').length
  const index = parseInt(el.dataset.index)
  const center = Math.floor(totalItems / 2)
  const distanceFromCenter = Math.abs(index - center)

  gsap.to(el, {
    opacity: 1,
    scale: 1,
    delay: distanceFromCenter * 0.1, // 更靠近中心的项目先动画
    onComplete: done
  })
}
```

## 性能考虑

- 保持单个动画持续时间短（200-500ms）
- 限制总交错时间（用户不应等待太久）
- 考虑使用 `will-change: transform, opacity` 以提高性能
- 对于大型列表，考虑仅对可见项目进行交错

```css
.list-item {
  will-change: transform, opacity;
}
```

## 参考
- [Vue.js TransitionGroup Staggering Transitions](https://vuejs.org/guide/built-ins/transition-group.html#staggering-list-transitions)
- [GSAP Animation Library](https://greensock.com/gsap/)
