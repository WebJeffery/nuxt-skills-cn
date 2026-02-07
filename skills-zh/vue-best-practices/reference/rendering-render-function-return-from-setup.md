---
title: 从 setup() 返回渲染函数，而不是直接 VNodes
impact: HIGH
impactDescription: 直接从 setup 返回 vnode 使其静态；返回函数启用响应式更新
type: gotcha
tags: [vue3, render-function, composition-api, setup, reactivity]
---

# 从 setup() 返回渲染函数，而不是直接 VNodes

**影响：高** - 将渲染函数与组合式 API 一起使用时，你必须返回一个返回 vnodes 的函数，而不是直接返回 vnodes。直接返回 vnodes 会创建一个静态渲染，当响应式状态更改时永远不会更新。

## 任务清单

- [ ] 使用渲染函数时始终从 setup() 返回箭头函数
- [ ] 永远不要直接从 setup() 返回 h() 调用
- [ ] 确保在返回的函数内访问响应式值

**错误：**
```js
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const increment = () => count.value++

    // 错误：返回静态 vnode，创建一次
    // 点击按钮更新 count.value，但 DOM 永远不会改变！
    return h('div', [
      h('p', `计数: ${count.value}`),  // 在 setup 时捕获 count.value (0)
      h('button', { onClick: increment }, '增加')
    ])
  }
}
```

**正确：**
```js
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const increment = () => count.value++

    // 正确：返回渲染函数
    // Vue 在每次响应式更新时调用此函数
    return () => h('div', [
      h('p', `计数: ${count.value}`),  // 每次渲染重新求值
      h('button', { onClick: increment }, '增加')
    ])
  }
}
```

## 为什么会发生这种情况

```js
// Vue 内部执行的操作：

// 错误方法 - setup 运行一次：
const result = setup()
// result 是一个 vnode { type: 'div', children: [...] }
// Vue 渲染一次，然后无法重新渲染

// 正确方法 - setup 返回一个函数：
const renderFn = setup()
// renderFn 是 () => h('div', ...)
// Vue 在挂载时调用 renderFn()
// Vue 在依赖项更改时再次调用 renderFn()
```

## 常见错误：混合模板和渲染函数

```vue
<script setup>
import { h, ref } from 'vue'

const count = ref(0)

// 错误：不能在带有模板的 script setup 中使用渲染函数
// 这个 h() 调用什么都不做
const node = h('div', count.value)
</script>

<template>
  <!-- 使用模板，忽略渲染函数 -->
  <div>{{ count }}</div>
</template>
```

如果你需要带有组合式 API 的渲染函数，不要使用 `<script setup>`：

```vue
<script>
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return () => h('div', count.value)
  }
}
</script>
<!-- 没有模板 - 使用渲染函数 -->
```

## 使用渲染函数时暴露值

```js
import { h, ref } from 'vue'

export default {
  setup(props, { expose }) {
    const count = ref(0)
    const reset = () => { count.value = 0 }

    // 为父组件引用暴露方法
    expose({ reset })

    // 仍然返回渲染函数
    return () => h('div', count.value)
  }
}
```

## 带有插槽

```js
import { h, ref } from 'vue'

export default {
  setup(props, { slots }) {
    const count = ref(0)

    return () => h('div', [
      h('p', `计数: ${count.value}`),
      // 插槽也必须在渲染函数内调用
      slots.default?.()
    ])
  }
}
```

## 参考
- [Vue.js 带有组合式 API 的渲染函数](https://vuejs.org/guide/extras/render-function.html#render-functions-jsx)
- [Vue.js 组合式 API setup()](https://vuejs.org/api/composition-api-setup.html)
