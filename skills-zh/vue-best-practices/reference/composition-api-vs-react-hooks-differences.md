---
title: Vue 组合式 API 运行一次,不像 React Hooks
impact: MEDIUM
impactDescription: 理解这种差异可以防止过度工程和不适用于 Vue 的 React 模式
type: gotcha
tags: [vue3, composition-api, react-hooks, setup, stale-closure]
---

# Vue 组合式 API 运行一次,不像 React Hooks

**影响:中等** - Vue 的 `setup()` 或 `<script setup>` 每个组件实例只执行一次,而 React Hooks 在每次渲染时运行。来自 React 的开发人员经常应用模式(依赖数组、过度记忆化、useCallback),这些模式在 Vue 中是不必要且适得其反的。

理解这一根本差异对于编写惯用的 Vue 代码至关重要。Vue 的方法消除了困扰 React 应用程序的整个类别的错误(过时的闭包、详尽的依赖)。

## 任务清单

- [ ] 不要实现"依赖数组" - Vue 自动跟踪依赖
- [ ] 不要将函数包装在 "useCallback" 等效项中 - Vue 中不需要
- [ ] 不要使用 "useMemo" 模式 - Vue 的 `computed()` 自动处理这个
- [ ] 理解 Vue 中的闭包不会像 React 中那样"过时"
- [ ] 不要担心"调用顺序" - Vue 组合式函数可以有条件

**Vue 中要避免的 React 模式:**
```javascript
// 这些模式在 Vue 中是不必要的 - 它们解决了 React 特定的问题

// 错误: 尝试实现依赖数组(React 模式)
watch(
  [dep1, dep2, dep3],  // Vue 在 watchEffect 中自动跟踪依赖
  () => {
    // ...
  }
)
// 除非您特别想控制哪些依赖触发侦听器,
// 否则首选自动跟踪的 watchEffect()

// 错误: 像 useCallback 一样记忆回调
const memoizedHandler = computed(() => {
  return () => doSomething(state.value)
})
// 在 Vue 中,只需正常定义函数 - 不需要记忆化

// 错误: 担心过时的闭包
function useData() {
  const data = ref(null)

  // 在 React 中,这可能会捕获过时的 'data' - 在 Vue 中不会!
  // Vue refs 总是当前的
  const handler = () => {
    console.log(data.value)  // 总是获取当前值
  }

  return { data, handler }
}
```

**正确的 Vue 模式:**
```javascript
import { ref, computed, watchEffect } from 'vue'

// 正确: 使用 watchEffect 自动依赖跟踪
const query = ref('')
const filter = ref('all')

watchEffect(() => {
  // Vue 自动检测这取决于 query 和 filter
  // 不需要依赖数组!
  fetchResults(query.value, filter.value)
})

// 正确: computed() 自动处理记忆化
const expensiveResult = computed(() => {
  // 仅当依赖实际更改时才重新计算
  return heavyComputation(data.value)
})

// 正确: 函数不需要记忆化
function handleClick() {
  count.value++
}
// 直接使用它 - 不需要 useCallback 包装
// <button @click="handleClick">

// 正确: 闭包总是访问当前值
const count = ref(0)
const message = ref('')

function logState() {
  // 这总是记录当前值,永远不会过时
  console.log(`Count: ${count.value}, Message: ${message.value}`)
}

setTimeout(() => {
  logState()  // 即使稍后调用也能获取当前值
}, 5000)
```

## Vue 相比 React Hooks 的优势

```javascript
// 1. 没有过时闭包问题
const count = ref(0)

onMounted(() => {
  setInterval(() => {
    // 在 React 中:需要 useRef 或依赖数组以避免过时值
    // 在 Vue 中:count.value 总是当前值
    console.log(count.value)
  }, 1000)
})

// 2. 组合式函数可以有条件
if (featureEnabled) {
  const { data } = useSomeFeature()  // 这在 Vue 中是可以的!
}
// 在 React 中:"Hooks 不能有条件" - 在 Vue 中不是问题

// 3. 没有详尽依赖 linting 头痛
watchEffect(() => {
  // 使用任何响应式值 - Vue 自动跟踪它们所有
  // 没有 ESLint 规则抱怨缺少依赖
  doSomething(a.value, b.value, c.value)
})

// 4. 子组件默认不需要记忆化
// Vue 的响应式系统只更新实际更改的内容
// 大多数情况下不需要 React.memo() 等效项
```

## Vue 模式何时不同

```javascript
// Setup 运行一次 - 所以初始化发生一次
<script setup>
import { ref, onMounted } from 'vue'

// 此代码在创建组件时运行一次
const data = ref(null)
console.log('Setup running')  // 仅记录一次

onMounted(() => {
  console.log('Mounted')  // 仅记录一次
})

// 如果您需要在每次响应式更改时运行某些内容,
// 使用 watch 或 watchEffect
watchEffect(() => {
  // 当依赖更改时运行
  console.log('Data changed:', data.value)
})
</script>
```

## 参考
- [组合式 API FAQ - 与 React Hooks 的关系](https://vuejs.org/guide/extras/composition-api-faq.html#relationship-with-react-hooks)
- [响应式基础](https://vuejs.org/guide/essentials/reactivity-fundamentals.html)
