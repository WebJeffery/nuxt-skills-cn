---
title: 组合式 API 使用可变响应式,而不是函数式编程
impact: MEDIUM
impactDescription: 误解范式会导致不正确的状态管理模式
type: gotcha
tags: [vue3, composition-api, reactivity, functional-programming, paradigm]
---

# 组合式 API 使用可变响应式,而不是函数式编程

**影响:中等** - 尽管基于函数,但组合式 API 遵循 Vue 的可变、细粒度响应式范式——而不是函数式编程原则。将其视为函数式范式会导致不正确的模式,如不必要的克隆、不可变样式更新或在变异是预期模式时避免变异。

Vue 的组合式 API 利用导入的函数来组织代码,但底层模型基于 Vue 跟踪和响应的可变响应式状态。这与函数式编程的不可变性(如 Redux reducers)根本不同。

## 任务清单

- [ ] 直接变异响应式状态 - 不要为每次更新创建新对象
- [ ] 不要不必要地应用不可变性模式(展开、用于更新的 Object.assign)
- [ ] 理解 `ref()` 和 `reactive()` 启用可变状态跟踪
- [ ] 按预期使用 Vue 的响应式:直接变异和自动跟踪

**不正确:**
```javascript
import { ref } from 'vue'

const todos = ref([])

// 错误: 将 Vue 视为 Redux/函数式 - 不必要的不可变性
function addTodo(todo) {
  // 每次都创建新数组在 Vue 中是浪费的
  todos.value = [...todos.value, todo]
}

function updateTodo(id, updates) {
  // 不必要的展开 - Vue 直接跟踪变异
  todos.value = todos.value.map(t =>
    t.id === id ? { ...t, ...updates } : t
  )
}

const user = ref({ name: 'John', age: 30 })

// 错误: 为简单更新创建新对象
function updateName(newName) {
  user.value = { ...user.value, name: newName }
}
```

**正确:**
```javascript
import { reactive, ref } from 'vue'

const todos = ref([])

// 正确: 直接变异 - Vue 跟踪更改
function addTodo(todo) {
  todos.value.push(todo) // 直接变异是 Vue 的方式
}

function updateTodo(id, updates) {
  const todo = todos.value.find(t => t.id === id)
  if (todo) {
    Object.assign(todo, updates) // 直接变异
  }
}

const user = ref({ name: 'John', age: 30 })

// 正确: 直接变异属性
function updateName(newName) {
  user.value.name = newName // Vue 跟踪这个!
}

// 或者使用 reactive():
const state = reactive({ name: 'John', age: 30 })

function updateNameReactive(newName) {
  state.name = newName // 直接变异,响应式保留
}
```

## 何时不可变性模式有意义

```javascript
// 不可变性在以下情况下是合适的:

// 1. 替换整个状态(例如,来自 API 响应)
const users = ref([])
async function fetchUsers() {
  users.value = await api.getUsers() // 完全替换是可以的
}

// 2. 当您需要快照进行比较时
const previousState = { ...currentState } // 用于撤销/重做

// 3. 将数据传递给期望不可变数据的外部库时
const chartData = computed(() => [...rawData.value]) // 为图表库复制
```

## Vue 心理模型

```javascript
// Vue 的响应式就像电子表格:
// - 单元格 A1 包含一个值(ref)
// - 单元格 B1 有一个引用 A1 的公式(computed)
// - 更改 A1,B1 自动更新

const a1 = ref(10)
const b1 = computed(() => a1.value * 2)

// 您更改 A1(变异),而不是创建新的 A1
a1.value = 20 // b1 自动变为 40

// 这与以下内容根本不同:
// state = reducer(state, action)  // 函数式/Redux 模式
```

## 参考
- [组合式 API FAQ](https://vuejs.org/guide/extras/composition-api-faq.html)
- [响应式基础](https://vuejs.org/guide/essentials/reactivity-fundamentals.html)
