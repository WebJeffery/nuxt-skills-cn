---
title: 渲染函数中的 VNodes 必须是唯一的
impact: HIGH
impactDescription: 重用 vnode 引用会导致渲染错误和意外行为
type: gotcha
tags: [vue3, render-function, vnode, composition-api]
---

# 渲染函数中的 VNodes 必须是唯一的

**影响：高** - 在渲染函数树中多次重用相同的 vnode 引用会导致渲染错误，其中只显示一个实例或更新行为异常。

组件渲染树中的每个 vnode 必须是唯一的。你不能多次使用相同的 vnode 对象。如果你需要多次渲染相同的元素，请使用工厂函数或通过在循环中调用 `h()` 来分别创建每个 vnode。

## 任务清单

- [ ] 永远不要将 vnode 存储在变量中并在同一树中多次使用
- [ ] 使用工厂函数或 `.map()` 创建多个类似的 vnodes
- [ ] 每次 `h()` 调用都会创建一个新的 vnode，因此为每个需要的实例调用它
- [ ] 将 vnode 创建提取到辅助函数时要特别小心

**错误：**
```javascript
import { h } from 'vue'

export default {
  setup() {
    return () => {
      // 错误：相同的 vnode 引用使用了两次
      const p = h('p', 'Hello')
      return h('div', [p, p]) // 错误！重复的 vnode 引用
    }
  }
}
```

```javascript
import { h } from 'vue'

export default {
  setup() {
    return () => {
      // 错误：在树的不同部分重用 vnode
      const icon = h('span', { class: 'icon' }, '★')
      return h('div', [
        h('button', [icon, ' Save']),    // 使用 icon
        h('button', [icon, ' Delete'])   // 重用相同的 icon - 错误！
      ])
    }
  }
}
```

**正确：**
```javascript
import { h } from 'vue'

export default {
  setup() {
    return () => {
      // 正确：为每次使用创建新的 vnode
      return h('div', [
        h('p', 'Hello'),
        h('p', 'Hello')
      ])
    }
  }
}
```

```javascript
import { h } from 'vue'

export default {
  setup() {
    return () => {
      // 正确：工厂函数每次创建新的 vnode
      const createIcon = () => h('span', { class: 'icon' }, '★')
      return h('div', [
        h('button', [createIcon(), ' Save']),
        h('button', [createIcon(), ' Delete'])
      ])
    }
  }
}
```

```javascript
import { h } from 'vue'

export default {
  setup() {
    return () => {
      // 正确：使用 map 创建多个 vnodes
      return h('div',
        Array.from({ length: 20 }).map(() => h('p', 'Hello'))
      )
    }
  }
}
```

```javascript
import { h } from 'vue'

export default {
  setup() {
    const items = ['Apple', 'Banana', 'Cherry']

    return () => h('ul',
      // 正确：每次迭代创建一个新的 vnode
      items.map((item, index) =>
        h('li', { key: index }, item)
      )
    )
  }
}
```

## 为什么 VNodes 必须是唯一的

VNodes 是轻量级 JavaScript 对象，Vue 的虚拟 DOM 算法使用它们进行 diff 和补丁。当相同的 vnode 引用出现多次时：
- Vue 无法区分实例
- diff 算法产生不正确的结果
- 可能只渲染一个实例，或者更新可能会破坏 DOM

每个 vnode 都维护自己的身份和树中的位置，这对于以下方面至关重要：
- 更新期间正确的 DOM 补丁
- 正确的生命周期钩子执行
- 列表中基于键的准确协调

## 参考
- [Vue.js Render Functions - Vnodes Must Be Unique](https://vuejs.org/guide/extras/render-function.html#vnodes-must-be-unique)
