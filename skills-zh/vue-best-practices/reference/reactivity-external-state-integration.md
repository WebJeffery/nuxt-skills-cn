---
title: 对外部状态库使用 shallowRef 模式
impact: MEDIUM
impactDescription: 外部状态系统（Immer、XState、Redux）应该使用 shallowRef 以避免在代理中双重包装
type: efficiency
tags: [vue3, reactivity, shallowRef, external-state, immer, xstate, integration]
---

# 对外部状态库使用 shallowRef 模式

**影响：中** - 将 Vue 与外部状态管理库（Immer、XState、Redux、MobX）集成时，使用 `shallowRef()` 来保存外部状态。这可以防止 Vue 在代理中深度包装外部状态，这可能导致冲突和性能问题。

模式：在 `shallowRef` 中保存外部状态，并在外部系统更新时完全替换 `.value`。这提供了 Vue 响应性，同时让外部库管理状态内部。

## 任务清单

- [ ] 使用 `shallowRef()` 保存外部库状态
- [ ] 当外部状态更改时完全替换 `.value`（不要变异）
- [ ] 集成产生新状态对象的更新函数
- [ ] 考虑将此模式用于不可变数据结构

**与 Immer 集成：**
```javascript
import { produce } from 'immer'
import { shallowRef } from 'vue'

export function useImmer(baseState) {
  const state = shallowRef(baseState)

  function update(updater) {
    // Immer 产生新的不可变状态
    // 完全替换 shallowRef 值以触发响应性
    state.value = produce(state.value, updater)
  }

  return [state, update]
}

// 用法
const [todos, updateTodos] = useImmer([
  { id: 1, text: 'Learn Vue', done: false }
])

// 使用 Immer 的可变 API 更新（产生不可变结果）
updateTodos(draft => {
  draft[0].done = true
  draft.push({ id:2, text: 'Use Immer', done: false })
})
```

**与 XState 集成：**
```javascript
import { createMachine, interpret } from 'xstate'
import { shallowRef, onUnmounted } from 'vue'

export function useMachine(options) {
  const machine = createMachine(options)
  const state = shallowRef(machine.initialState)

  const service = interpret(machine)
    .onTransition((newState) => {
      // 在每次转换时完全替换状态
      state.value = newState
    })
    .start()

  const send = (event) => service.send(event)

  onUnmounted(() => service.stop())

  return { state, send }
}

// 用法
const { state, send } = useMachine({
  id: 'toggle',
  initial: 'inactive',
  states: {
    inactive: { on: { TOGGLE: 'active' } },
    active: { on: { TOGGLE: 'inactive' } }
  }
})

// 在模板中：state.value.matches('active')
send('TOGGLE')
```

**与 Redux 风格的存储集成：**
```javascript
import { shallowRef, readonly } from 'vue'

export function createStore(reducer, initialState) {
  const state = shallowRef(initialState)

  function dispatch(action) {
    state.value = reducer(state.value, action)
  }

  function getState() {
    return state.value
  }

  return {
    state: readonly(state),  // 防止直接变异
    dispatch,
    getState
  }
}

// 用法
const store = createStore(
  (state, action) => {
    switch (action.type) {
      case 'INCREMENT':
        return { ...state, count: state.count + 1 }
      default:
        return state
    }
  },
  { count: 0 }
)

store.dispatch({ type: 'INCREMENT' })
console.log(store.state.value.count) // 1
```

**为什么不对外部状态使用 ref()：**
```javascript
import { ref } from 'vue'
import { produce } from 'immer'

// 错误：ref() 深度包装状态
const state = ref({ nested: { value: 1 } })

// 这创建了双重代理：
// 1. Vue 在 Proxy 中包装状态
// 2. 外部库也可能包装/期望原始对象
// 3. 导致标识问题和潜在冲突

// 错误：使用 Immer 变异 ref
state.value = produce(state.value, draft => {
  draft.nested.value = 2
})
// state.value 上的 Vue 深度代理可能会干扰 Immer 的代理
```

**使用 shallowRef 的正确模式：**
```javascript
import { shallowRef } from 'vue'

// 正确：shallowRef 只跟踪 .value 替换
const state = shallowRef({ nested: { value: 1 } })

// 外部库在内部使用原始对象工作
state.value = produce(state.value, draft => {
  draft.nested.value = 2
})
// 清晰分离：Vue 跟踪外部 ref，库管理内部状态
```

## 参考
- [Vue.js 响应式深入 - 与外部状态集成](https://vuejs.org/guide/extras/reactivity-in-depth.html#integration-with-external-state-systems)
- [Vue.js shallowRef API](https://vuejs.org/api/reactivity-advanced.html#shallowref)
- [Immer 文档](https://immerjs.github.io/immer/)
- [XState 文档](https://xstate.js.org/)
