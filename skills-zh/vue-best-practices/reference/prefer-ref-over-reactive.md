---
title: 为一致性首选 ref() 而不是 reactive()
impact: MEDIUM
impactDescription: 使用 ref() 作为默认值可以避免 reactive() 陷阱并提供一致的模式
type: efficiency
tags: [vue3, reactivity, ref, reactive, composition-api, best-practice]
---

# 为一致性首选 ref() 而不是 reactive()

**影响：中** - Vue 文档推荐使用 `ref()` 作为声明响应式状态的主要 API。这避免了几个 `reactive()` 陷阱，并在你的代码库中提供了一致的模式。

虽然 `ref()` 和 `reactive()` 都创建响应式状态，但 `reactive()` 有几个限制：它只适用于对象（不适用于原始值），不能重新赋值，并且在解构时会丢失响应性。一致地使用 `ref()` 意味着只需要记住一种模式。

## 任务清单

- [ ] 将 `ref()` 用作所有响应式状态的默认值
- [ ] 只有当你有特定原因时才使用 `reactive()`（例如，一组相关状态）
- [ ] 在代码库中保持一致 - 选择一种方法并坚持使用
- [ ] 记住：`.value` 是避免 `reactive()` 陷阱的代价

**错误：**
```javascript
import { reactive } from 'vue'

// reactive() 有多个陷阱：

// 1. 不能与原始值一起使用
const count = reactive(0)  // 不会工作 - 不是响应式的

// 2. 不能重新赋值整个对象
let state = reactive({ items: [] })
state = reactive({ items: [1, 2, 3] })  // 丢失响应性！

// 3. 解构破坏响应性
const { items } = state  // items 不是响应式的

// 4. 传递给函数可能会丢失响应性
someFunction(state.items)  // 根据使用情况可能会丢失响应性
```

**正确：**
```javascript
import { ref } from 'vue'

// ref() 普遍适用：

// 1. 与原始值一起工作
const count = ref(0)
count.value++  // 有效！

// 2. 可以重新赋值整个对象
const state = ref({ items: [] })
state.value = { items: [1, 2, 3] }  // 响应性已保留！

// 3. 没有解构问题（你使用 .value）
const items = state.value.items  // 如果你只需要值

// 4. 传递 refs 是显式的
someFunction(state)        // 传递 ref
someFunction(state.value)  // 或者显式传递值
```

```javascript
// 当 reactive() 有意义时：分组相关状态
import { reactive, toRefs } from 'vue'

// 可接受的用例：具有许多相关字段的表单状态
const form = reactive({
  username: '',
  email: '',
  password: '',
  confirmPassword: ''
})

// 但如果你需要解构，始终使用 toRefs()
const { username, email } = toRefs(form)
```

## 参考
- [Vue.js 响应式基础](https://vuejs.org/guide/essentials/reactivity-fundamentals.html)
- [Vue.js Composition API 常见问题](https://vuejs.org/guide/extras/composition-api-faq.html)
