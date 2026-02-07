---
title: 避免使用 === 运算符比较响应式对象
impact: HIGH
impactDescription: 响应式代理具有与原始对象不同的标识 - 比较错误是静默的且难以调试
type: gotcha
tags: [vue3, reactivity, proxy, comparison, debugging, identity]
---

# 避免使用 === 运算符比较响应式对象

**影响：高** - Vue 的 `reactive()` 返回一个与原始对象具有不同标识的 Proxy 包装器。使用 `===` 比较响应式对象可能导致静默错误，比较意外返回 `false`。

当你使用 `reactive()` 包装对象时，返回的代理不等于原始对象。此外，从响应式对象访问嵌套对象每次都会返回新的代理包装器，这可能导致标识比较问题。

## 任务清单

- [ ] 永远不要直接使用 `===` 比较响应式对象实例
- [ ] 使用唯一标识符（ID、UUID）进行对象比较
- [ ] 当绝对需要标识比较时，在两边使用 `toRaw()`
- [ ] 考虑使用数据库记录中的原始标识符进行比较

**错误：**
```javascript
import { reactive } from 'vue'

const original = { id: 1, name: 'Item' }
const state = reactive(original)

// 错误：始终返回 false - proxy !== original
if (state === original) {
  console.log('Same object') // 永远不会执行
}

// 错误：嵌套对象比较失败
const items = reactive([{ id: 1 }, { id: 2 }])
const item = items[0]

// 稍后...
if (items[0] === item) {
  // 可能工作也可能不工作，取决于 Vue 的代理缓存
}

// 错误：比较来自不同响应式源的项目
const listA = reactive([{ id: 1 }])
const listB = reactive([{ id: 1 }])
if (listA[0] === listB[0]) {
  // 永远不会为真，即使它们代表相同的数据
}
```

**正确：**
```javascript
import { reactive, toRaw } from 'vue'

const original = { id: 1, name: 'Item' }
const state = reactive(original)

// 正确：使用 toRaw() 进行标识比较
if (toRaw(state) === original) {
  console.log('Same underlying object') // 有效！
}

// 最好：改用唯一标识符
const items = reactive([
  { id: 'uuid-1', name: 'Item 1' },
  { id: 'uuid-2', name: 'Item 2' }
])

function findItem(targetId) {
  return items.find(item => item.id === targetId)
}

function isSelected(item) {
  return selectedId.value === item.id // 比较 ID，而不是对象
}

// 正确：对于 Set/Map 操作，使用原始键
const selectedIds = reactive(new Set())
selectedIds.add(item.id)  // 使用 ID，而不是对象
selectedIds.has(item.id)  // 通过 ID 检查
```

```javascript
// 当必须比较对象时，在两边使用 toRaw
import { toRaw, isReactive } from 'vue'

function areEqual(a, b) {
  const rawA = isReactive(a) ? toRaw(a) : a
  const rawB = isReactive(b) ? toRaw(b) : b
  return rawA === rawB
}
```

## 参考
- [Vue.js 响应式深入](https://vuejs.org/guide/extras/reactivity-in-depth.html)
- [Vue.js toRaw() API](https://vuejs.org/api/reactivity-advanced.html#toraw)
