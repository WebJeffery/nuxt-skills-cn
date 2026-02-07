---
title: 遵循 Composable 命名约定和返回模式
impact: MEDIUM
impactDescription: 不一致的 composable 模式导致令人困惑的 API 和解构时的响应式问题
type: best-practice
tags: [vue3, composables, composition-api, naming, conventions, refs]
---

# 遵循 Composable 命名约定和返回模式

**影响: 中等** - Vue composables 应该遵循既定约定：使用 "use" 前缀命名并返回包含 refs（而不是响应式对象）的普通对象。返回响应式对象会在解构时导致响应式丢失，而不一致的命名使代码更难理解。

## 任务清单

- [ ] 使用 "use" 前缀命名 composables（例如，`useMouse`、`useFetch`、`useAuth`）
- [ ] 返回包含 refs 的普通对象，而不是响应式对象
- [ ] 允许解构和对象样式访问
- [ ] 为使用者记录返回的 refs

**不正确:**
```javascript
// 错误: 没有 "use" 前缀 - 不清楚它是 composable
export function mousePosition() {
  const x = ref(0)
  const y = ref(0)
  return { x, y }
}

// 错误: 返回响应式对象 - 解构会丢失响应式
export function useMouse() {
  const state = reactive({
    x: 0,
    y: 0
  })
  // 当使用者解构时: const { x, y } = useMouse()
  // x 和 y 变成普通值，不是响应式的！
  return state
}

// 错误: 直接返回单个 ref - 不一致的 API
export function useCounter() {
  const count = ref(0)
  return count  // 使用者必须在任何地方使用 .value
}
```

**正确:**
```javascript
// 正确: "use" 前缀并返回包含 refs 的普通对象
export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // 返回包含 refs 的普通对象
  return { x, y }
}

// 使用者可以解构并保持响应式
const { x, y } = useMouse()
watch(x, (newX) => console.log('x 已更改:', newX))  // 有效！

// 或者如果更喜欢，作为对象使用
const mouse = useMouse()
console.log(mouse.x.value)
```

## 使用 reactive() 包装器进行自动解包

如果使用者更喜欢自动解包（没有 `.value`），他们可以包装结果：

```javascript
import { reactive } from 'vue'
import { useMouse } from './composables/useMouse'

// 包装在 reactive() 中链接 refs
const mouse = reactive(useMouse())

// 现在没有 .value 的情况下访问
console.log(mouse.x)  // 自动解包，仍然响应式

// 但不要从中解构！
const { x } = reactive(useMouse())  // 错误: 再次丢失响应式
```

## 模式: 返回状态和操作

```javascript
// 带有状态和方法的 Composable
export function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  const doubleCount = computed(() => count.value * 2)

  function increment() {
    count.value++
  }

  function decrement() {
    count.value--
  }

  function reset() {
    count.value = initialValue
  }

  // 在普通对象中返回所有 refs 和函数
  return {
    count,
    doubleCount,
    increment,
    decrement,
    reset
  }
}

// 用法
const { count, doubleCount, increment, reset } = useCounter(10)
```

## 命名约定示例

| 好名称 | 坏名称 | 原因 |
|-----------|----------|--------|
| `useFetch` | `fetch` | 与原生 fetch 冲突 |
| `useAuth` | `authStore` | "Store" 暗示 Pinia/Vuex |
| `useLocalStorage` | `localStorage` | 与原生 API 冲突 |
| `useFormValidation` | `validateForm` | 听起来像一次性函数 |
| `useWindowSize` | `getWindowSize` | "get" 暗示同步 getter |

## 参考
- [Vue.js Composables - 约定和最佳实践](https://vuejs.org/guide/reusability/composables.html#conventions-and-best-practices)
- [Vue.js Composables - 返回值](https://vuejs.org/guide/reusability/composables.html#return-values)
