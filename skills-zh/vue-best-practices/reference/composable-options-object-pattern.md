---
title: 对 Composable 参数使用选项对象模式
impact: MEDIUM
impactDescription: 长参数列表容易出错且不清楚；选项对象是自文档和可扩展的
type: best-practice
tags: [vue3, composables, composition-api, api-design, typescript, patterns]
---

# 对 Composable 参数使用选项对象模式

**影响: 中等** - 当 composable 接受多个参数（尤其是可选参数）时，使用选项对象而不是位置参数。这使 API 自文档化，防止参数顺序错误，并允许在不破坏更改的情况下轻松扩展。

## 任务清单

- [ ] 当 composable 有超过 2-3 个参数时使用选项对象
- [ ] 当大多数参数是可选时始终使用选项对象
- [ ] 通过解构提供合理的默认值
- [ ] 为选项对象类型以获得更好的 IDE 支持
- [ ] 必需参数可以是位置参数；可选参数在选项中

**不正确:**
```javascript
// 错误: 许多位置参数 - 不清楚且容易出错
export function useFetch(url, method, headers, timeout, retries, onError) {
  // 第 4 个参数又是什么？
}

// 用法 - 哪个布尔值是哪个？
const { data } = useFetch('/api/users', 'GET', null, 5000, 3, handleError)

// 错误: 容易弄错顺序
export function useDebounce(value, delay, immediate, maxWait) {
  // ...
}

// 500 是延迟还是 maxWait？true 是 immediate 吗？
const debounced = useDebounce(searchQuery, 500, true, 1000)
```

**正确:**
```javascript
// 正确: 选项对象模式
export function useFetch(url, options = {}) {
  const {
    method = 'GET',
    headers = {},
    timeout = 30000,
    retries = 0,
    onError = null,
    immediate = true
  } = options

  // 实现...
}

// 用法 - 清晰且自文档化
const { data } = useFetch('/api/users', {
  method: 'POST',
  timeout: 5000,
  retries: 3,
  onError: handleError
})

// 正确: 使用 TypeScript 以获得更好的 IDE 支持
interface UseFetchOptions {
  method?: 'GET' | 'POST' | 'PUT' | 'DELETE'
  headers?: Record<string, string>
  timeout?: number
  retries?: number
  onError?: (error: Error) => void
  immediate?: boolean
}

export function useFetch(url: MaybeRefOrGetter<string>, options: UseFetchOptions = {}) {
  const {
    method = 'GET',
    headers = {},
    timeout = 30000,
    retries = 0,
    onError = null,
    immediate = true
  } = options

  // TypeScript 现在为选项提供自动完成
}
```

## 模式: 必需 + 选项

保持真正必需的参数为位置参数，捆绑可选参数：

```javascript
// url 始终是必需的，选项不是
export function useFetch(url, options = {}) {
  // ...
}

// key 和 storage 都是必需的，这才有意义
export function useStorage(key, storage, options = {}) {
  const { serializer = JSON, deep = true } = options
  // ...
}

// 用法
useStorage('user-prefs', localStorage, { deep: false })
```

## 模式: 响应式选项

选项也可以是响应式的以实现动态行为：

```javascript
export function useFetch(url, options = {}) {
  const {
    refetch = ref(true),  // 可以是一个 ref！
    interval = null
  } = options

  watchEffect(() => {
    if (toValue(refetch)) {
      // 执行获取
    }
  })
}

// 使用响应式选项
const shouldFetch = ref(true)
const { data } = useFetch('/api/data', { refetch: shouldFetch })

// 稍后，禁用获取
shouldFetch.value = false
```

## 模式: 返回配置

选项对象也适用于返回值：

```javascript
export function useCounter(options = {}) {
  const { initial = 0, min = -Infinity, max = Infinity, step = 1 } = options

  const count = ref(initial)

  function increment() {
    count.value = Math.min(count.value + step, max)
  }

  function decrement() {
    count.value = Math.max(count.value - step, min)
  }

  function set(value) {
    count.value = Math.min(Math.max(value, min), max)
  }

  return { count, increment, decrement, set }
}

// 清晰、可读的用法
const { count, increment, decrement } = useCounter({
  initial: 10,
  min: 0,
  max: 100,
  step: 5
})
```

## VueUse 约定

VueUse 广泛使用此模式：

```javascript
import { useDebounceFn, useThrottleFn, useLocalStorage } from '@vueuse/core'

// 所有都使用选项对象
const debouncedFn = useDebounceFn(fn, 1000, { maxWait: 5000 })

const throttledFn = useThrottleFn(fn, 1000, { trailing: true, leading: false })

const state = useLocalStorage('key', defaultValue, {
  deep: true,
  listenToStorageChanges: true,
  serializer: {
    read: JSON.parse,
    write: JSON.stringify
  }
})
```

## 反模式: 布尔陷阱

选项对象防止"布尔陷阱"：

```javascript
// 坏: 这些布尔值是什么意思？
useModal(true, false, true)

// 好: 自文档化
useModal({
  closable: true,
  backdrop: false,
  keyboard: true
})
```

## 参考
- [Vue.js Composables](https://vuejs.org/guide/reusability/composables.html)
- [VueUse Composables](https://vueuse.org/) - 选项模式示例
- [Vue Composables 的良好实践](https://dev.to/jacobandrewsky/good-practices-and-design-patterns-for-vue-composables-24lk)
