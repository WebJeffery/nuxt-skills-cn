---
title: 计算属性 getter 必须没有副作用
impact: HIGH
impactDescription: 计算属性 getter 中的副作用破坏响应性并导致不可预测的行为
type: efficiency
tags: [vue3, computed, reactivity, side-effects, best-practices]
---

# 计算属性 getter 必须没有副作用

**影响：高** - 计算 getter 函数应该只执行纯计算。计算属性 getter 中的副作用破坏 Vue 的响应式模型，并导致难以跟踪的错误。

计算属性旨在声明式地描述如何从其他响应式状态派生值。它们不用于执行操作或修改状态。

## 任务清单

- [ ] 永远不要在计算属性 getter 中变异其他响应式状态
- [ ] 永远不要在计算属性 getter 中进行异步请求或 API 调用
- [ ] 永远不要在计算属性 getter 中执行 DOM 变异
- [ ] 使用监听器对具有副作用的状态更改做出反应
- [ ] 使用事件处理程序进行用户触发的操作

**错误：**
```vue
<script setup>
import { ref, computed } from 'vue'

const items = ref([])
const count = ref(0)
const lastFetch = ref(null)

// 错误：变异其他状态
const doubledCount = computed(() => {
  count.value++  // 副作用 - 修改状态！
  return count.value * 2
})

// 错误：进行异步请求
const userData = computed(async () => {
  const response = await fetch('/api/user')  // 副作用 - API 调用！
  return response.json()
})

// 错误：修改 DOM
const highlightedItems = computed(() => {
  document.title = `${items.value.length} items`  // 副作用 - DOM 变异！
  return items.value.filter(i => i.highlighted)
})

// 错误：写入外部状态
const processedData = computed(() => {
  lastFetch.value = new Date()  // 副作用 - 修改状态！
  return items.value.map(i => i.name)
})
</script>
```

**正确：**
```vue
<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const items = ref([])
const count = ref(0)
const userData = ref(null)

// 正确：仅纯计算
const doubledCount = computed(() => {
  return count.value * 2
})

// 正确：使用生命周期钩子进行初始获取
onMounted(async () => {
  const response = await fetch('/api/user')
  userData.value = await response.json()
})

// 正确：纯过滤
const highlightedItems = computed(() => {
  return items.value.filter(i => i.highlighted)
})

// 正确：使用监听器进行副作用
watch(items, (newItems) => {
  document.title = `${newItems.length} items`
}, { immediate: true })

// 通过事件处理程序递增计数，而不是计算属性
function increment() {
  count.value++
}
</script>
```

## 什么算作副作用

| 副作用类型 | 示例 | 替代方案 |
|-----------------|---------|-------------|
| 状态变异 | `otherRef.value = x` | 使用监听器 |
| API 调用 | `fetch()`, `axios()` | 使用监听器或生命周期钩子 |
| DOM 操作 | `document.title = x` | 使用监听器 |
| 控制台日志 | `console.log()` | 删除或使用监听器 |
| 存储访问 | `localStorage.setItem()` | 使用监听器 |
| 定时器设置 | `setTimeout()` | 使用生命周期钩子 |

## 参考
- [Vue.js 计算属性 - getter 应该没有副作用](https://vuejs.org/guide/essentials/computed.html#getters-should-be-side-effect-free)
