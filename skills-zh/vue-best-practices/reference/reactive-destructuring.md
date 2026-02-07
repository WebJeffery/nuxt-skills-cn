---
title: 永远不要直接解构 reactive() 对象
impact: HIGH
impactDescription: 解构响应式对象会破坏响应性 - 更改不会触发更新
type: capability
tags: [vue3, reactivity, reactive, composition-api, destructuring]
---

# 永远不要直接解构 reactive() 对象

**影响：高** - 解构 `reactive()` 对象会破坏响应式连接。对解构变量的更新不会触发 UI 更新，导致显示过时数据。

Vue 的 `reactive()` 使用 JavaScript Proxies 来跟踪属性访问。当你解构时，你从代理中提取原始值，失去响应式连接。当从 composables 或导入的状态解构时，这尤其危险。

## 任务清单

- [ ] 如果你需要响应性，永远不要直接解构响应式对象
- [ ] 在解构之前使用 `toRefs()` 将响应式对象属性转换为 refs
- [ ] 考虑使用 `ref()` 而不是 `reactive()` 来完全避免这个陷阱
- [ ] 从 composables 导入状态时，在解构之前检查它是否是响应式的

**错误：**
```javascript
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  name: 'Vue'
})

// 错误：解构破坏响应性
const { count, name } = state

// 这些更新在原始状态上有效...
state.count++  // state.count 现在是 1

// ...但解构的变量不会更新
console.log(count)  // 仍然是 0！丢失了响应性
```

```javascript
// 错误：从 composable 解构
function useCounter() {
  const state = reactive({ count: 0 })
  return state
}

const { count } = useCounter()  // count 现在是一个非响应式的原始值
```

**正确：**
```javascript
import { reactive, toRefs } from 'vue'

const state = reactive({
  count: 0,
  name: 'Vue'
})

// 正确：使用 toRefs() 来保持响应性
const { count, name } = toRefs(state)

state.count++
console.log(count.value)  // 1 - 响应性已保留！（注意：现在需要 .value）
```

```javascript
// 正确：从 composables 返回 toRefs
function useCounter() {
  const state = reactive({ count: 0 })
  return toRefs(state)  // 现在可以安全地解构
}

const { count } = useCounter()  // count 现在是一个 ref，响应性已保留
```

```javascript
// 替代方案：直接使用 ref() 来完全避免问题
import { ref } from 'vue'

const count = ref(0)
const name = ref('Vue')

// 不需要解构，没有陷阱
```

## 参考
- [Vue.js 响应式基础 - reactive()](https://vuejs.org/guide/essentials/reactivity-fundamentals.html#reactive)
- [Vue.js 响应式 API - toRefs()](https://vuejs.org/api/reactivity-utilities.html#torefs)
