---
title: 避免在计算属性中对数组使用变异方法
impact: HIGH
impactDescription: 计算属性中的数组变异方法会修改源数据，导致意外行为
type: capability
tags: [vue3, computed, arrays, mutation, sort, reverse]
---

# 避免在计算属性中对数组使用变异方法

**影响：高** - JavaScript 数组方法如 `reverse()`、`sort()`、`splice()`、`push()`、`pop()`、`shift()` 和 `unshift()` 会变异原始数组。在计算属性中直接在响应式数组上使用它们会修改你的源数据，导致意外的副作用和错误。

## 任务清单

- [ ] 在使用变异方法之前始终创建数组的副本
- [ ] 使用展开运算符 `[...array]` 或 `slice()` 复制数组
- [ ] 如果可用，首选非变异替代方案
- [ ] 了解哪些数组方法变异 vs 返回新数组

**错误：**
```vue
<script setup>
import { ref, computed } from 'vue'

const items = ref([3,1, 4, 1,5, 9, 2, 6])
const users = ref([
  { name: 'Alice', age: 30 },
  { name: 'Bob', age: 25 }
])

// 错误：sort() 变异原始数组！
const sortedItems = computed(() => {
  return items.value.sort((a, b) => a - b)
})

// 错误：reverse() 变异原始数组！
const reversedItems = computed(() => {
  return items.value.reverse()
})

// 错误：两个数组现在指向相同的变异数据
// items.value 和 sortedItems.value 是同一个数组
// items.value 和 reversedItems.value 是同一个数组

// 错误：链式变异
const sortedUsers = computed(() => {
  return users.value.sort((a, b) => a.age - b.age)
})
</script>

<template>
  <!-- 原始数组已损坏！-->
  <div>Original: {{ items }}</div>
  <div>Sorted: {{ sortedItems }}</div>
</template>
```

**正确：**
```vue
<script setup>
import { ref, computed } from 'vue'

const items = ref([3,1, 4, 1,5, 9, 2, 6])
const users = ref([
  { name: 'Alice', age: 30 },
  { name: 'Bob', age: 25 }
])

// 正确：展开运算符首先创建副本
const sortedItems = computed(() => {
  return [...items.value].sort((a, b) => a - b)
})

// 正确：slice() 也创建副本
const reversedItems = computed(() => {
  return items.value.slice().reverse()
})

// 正确：在排序对象之前复制
const sortedUsers = computed(() => {
  return [...users.value].sort((a, b) => a.age - b.age)
})

// 正确：使用 toSorted() (ES2023) - 非变异
const sortedItemsModern = computed(() => {
  return items.value.toSorted((a, b) => a - b)
})

// 正确：使用 toReversed() (ES2023) - 非变异
const reversedItemsModern = computed(() => {
  return items.value.toReversed()
})
</script>

<template>
  <!-- 原始数组保持完整 -->
  <div>Original: {{ items }}</div>
  <div>Sorted: {{ sortedItems }}</div>
  <div>Reversed: {{ reversedItems }}</div>
</template>
```

## 变异 vs 非变异数组方法

| 变异（在计算属性中避免） | 非变异（安全） |
|------------------------------|---------------------|
| `sort()` | `toSorted()` (ES2023) |
| `reverse()` | `toReversed()` (ES2023) |
| `splice()` | `toSpliced()` (ES2023) |
| `push()` | `concat()` |
| `pop()` | `slice(0, -1)` |
| `shift()` | `slice(1)` |
| `unshift()` | `[item, ...array]` |
| `fill()` | `map()` 带有新值 |

## ES2023 非变异替代方案

现代 JavaScript (ES2023) 提供了常见数组方法的非变异版本：

```javascript
// 这些返回新数组，对计算属性安全
const sorted = array.toSorted((a, b) => a - b)
const reversed = array.toReversed()
const spliced = array.toSpliced(1, 2, 'new')
const withReplaced = array.with(0, 'newFirst')
```

## 嵌套数组的深拷贝

对于可能变异嵌套属性的对象数组：

```javascript
const items = ref([{ name: 'A', values: [1, 2, 3] }])

// 浅拷贝 - 嵌套数组仍然共享
const copied = computed(() => [...items.value])

// 如果你需要变异嵌套结构，则深拷贝
const deepCopied = computed(() => {
  return JSON.parse(JSON.stringify(items.value))
  // 或者使用 structuredClone()：
  // return structuredClone(items.value)
})
```

## 参考
- [Vue.js 计算属性 - 避免变异计算值](https://vuejs.org/guide/essentials/computed.html#avoid-mutating-computed-value)
- [MDN 数组方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
