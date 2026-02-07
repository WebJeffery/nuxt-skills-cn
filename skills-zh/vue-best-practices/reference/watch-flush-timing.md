---
title: 在监听器中访问更新的 DOM 时使用 flush post
impact: MEDIUM
impactDescription: 默认监听器时序在 DOM 更新之前运行，导致过时的 DOM 读取
type: capability
tags: [vue3, watch, watchers, flush, DOM, timing, post]
---

# 在监听器中访问更新的 DOM 时使用 flush: 'post'

**影响：中** - 默认情况下，监听器回调在组件的 DOM 更新之前运行。如果你在监听器回调中访问 DOM，你将看到更新前的状态。当你需要访问更新的 DOM 时，使用 `flush: 'post'` 或 `watchPostEffect`。

## 任务清单

- [ ] 在响应式状态更改后读取 DOM 时使用 `{ flush: 'post' }`
- [ ] 使用 `watchPostEffect()` 作为带有 flush: 'post' 的 `watchEffect` 的简写
- [ ] 除非绝对必要，否则避免 `{ flush: 'sync' }`（性能影响）
- [ ] 记住默认时序对于大多数非 DOM 操作是理想的

**错误：**
```vue
<script setup>
import { ref, watch, watchEffect } from 'vue'

const count = ref(0)
const listItems = ref(['a', 'b', 'c'])

// 错误：DOM 在此运行时显示旧值
watch(count, () => {
  // 元素仍然显示旧的 count 值
  const el = document.querySelector('.counter')
  console.log('DOM 显示：', el.textContent)  // 旧值！
})

// 错误：列表 DOM 尚未更新
watchEffect(() => {
  console.log('项目：', listItems.value.length)
  // DOM 仍然有旧数量的列表项
  const items = document.querySelectorAll('.list-item')
  console.log('DOM 项目：', items.length)  // 旧计数！
})
</script>

<template>
  <div class="counter">{{ count }}</div>
  <ul>
    <li v-for="item in listItems" :key="item" class="list-item">
      {{ item }}
    </li>
  </ul>
</template>
```

**正确：**
```vue
<script setup>
import { ref, watch, watchEffect, watchPostEffect } from 'vue'

const count = ref(0)
const listItems = ref(['a', 'b', 'c'])

// 正确：flush: 'post' 在 DOM 更新后运行
watch(
  count,
  () => {
    const el = document.querySelector('.counter')
    console.log('DOM 显示：', el.textContent)  // 正确的新值！
  },
  { flush: 'post' }
)

// 正确：watchPostEffect 简写
watchPostEffect(() => {
  console.log('项目：', listItems.value.length)
  const items = document.querySelectorAll('.list-item')
  console.log('DOM 项目：', items.length)  // 与 listItems.length 匹配！
})

// 正确：使用带有 flush 选项的 watchEffect
watchEffect(
  () => {
    // 一起访问响应式状态和 DOM
    const expectedCount = listItems.value.length
    const actualCount = document.querySelectorAll('.list-item').length
    console.log(`预期：${expectedCount}，实际：${actualCount}`)
  },
  { flush: 'post' }
)
</script>

<template>
  <div class="counter">{{ count }}</div>
  <ul>
    <li v-for="item in listItems" :key="item" class="list-item">
      {{ item }}
    </li>
  </ul>
</template>
```

## Flush 时序选项

```javascript
import { watch, watchEffect, watchPostEffect, watchSyncEffect } from 'vue'

// 默认：'pre' - 在组件 DOM 更新之前运行
watch(source, callback)  // 与 { flush: 'pre' } 相同

// Post：在组件 DOM 更新之后运行
watch(source, callback, { flush: 'post' })
watchPostEffect(callback)  // 简写

// Sync：当响应式值更改时立即运行
// 谨慎使用 - 没有批处理，对每个变异触发
watch(source, callback, { flush: 'sync' })
watchSyncEffect(callback)  // 简写
```

## 何时使用每种 Flush 时序

| 时序 | 用例 |
|--------|----------|
| `'pre'` (默认) | 不需要 DOM 访问的逻辑 |
| `'post'` | 读取或测量更新的 DOM |
| `'sync'` | 仅用于调试日志、简单的布尔标志 |

## 同步监听器警告

```javascript
import { ref, watch } from 'vue'

const items = ref([1, 2, 3])

// 危险：对每个数组变异触发
watch(
  items,
  () => {
    console.log('已更改！')  // 对 push、push、push 调用 3 次
  },
  { flush: 'sync' }
)

// 这会同步触发监听器 3 次
items.value.push(4)
items.value.push(5)
items.value.push(6)

// 更好：使用默认的 flush 来批处理更新
watch(items, () => {
  console.log('已更改！')  // 在所有变异后调用一次
}, { deep: true })
```

## 实际示例：自动滚动

```vue
<script setup>
import { ref, watchPostEffect } from 'vue'

const messages = ref([])
const containerRef = ref(null)

// 当新消息到达时自动滚动到底部
watchPostEffect(() => {
  // 访问 messages.value 以跟踪它
  const msgCount = messages.value.length

  // DOM 已更新，可以安全滚动
  if (containerRef.value && msgCount > 0) {
    containerRef.value.scrollTop = containerRef.value.scrollHeight
  }
})

function addMessage(text) {
  messages.value.push({ text, timestamp: Date.now() })
}
</script>

<template>
  <div ref="containerRef" class="messages">
    <div v-for="msg in messages" :key="msg.timestamp">
      {{ msg.text }}
    </div>
  </div>
</template>
```

## 参考
- [Vue.js 监听器 - 回调 Flush 时序](https://vuejs.org/guide/essentials/watchers.html#callback-flush-timing)
