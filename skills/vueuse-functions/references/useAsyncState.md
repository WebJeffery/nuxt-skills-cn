---
category: State
---

# useAsyncState

响应式异步状态。不会阻塞您的 setup 函数,并且会在 promise 准备好时触发更改。默认情况下,state 是一个 `shallowRef`。

## 用法

```ts
import { useAsyncState } from '@vueuse/core'
import axios from 'axios'

const { state, isReady, isLoading, error } = useAsyncState(
  axios
    .get('https://jsonplaceholder.typicode.com/todos/1')
    .then(t => t.data),
  { id: null },
)
```

### 返回值

| 属性             | 描述                                         |
| ------------------ | --------------------------------------------------- |
| `state`            | 异步函数的结果                    |
| `isReady`          | 当 promise 至少解析一次时为 `true`  |
| `isLoading`        | 当 promise 待处理时为 `true`                 |
| `error`            | 如果 promise 被拒绝,则为错误               |
| `execute`          | 使用可选延迟重新执行异步函数   |
| `executeImmediate` | 立即重新执行(`execute(0)` 的简写)  |

### 等待结果

返回值是 thenable,因此您可以在异步函数或 `<script setup>` 中等待它:

```ts
const { state, isReady } = await useAsyncState(fetchData, null)
// `state` 现在已填充,`isReady` 为 true
```

### 手动执行

设置 `immediate: false` 以防止在创建时自动执行。

```vue
<script setup lang="ts">
import { useAsyncState } from '@vueuse/core'

const { state, execute, executeImmediate } = useAsyncState(action, '', { immediate: false })

async function action(event) {
  await new Promise(resolve => setTimeout(resolve, 500))
  return `${event.target.textContent} clicked!`
}
</script>

<template>
  <p>State: {{ state }}</p>

  <button class="button" @click="executeImmediate">
    立即执行
  </button>

  <button class="ml-2 button" @click="event => execute(500, event)">
    延迟执行
  </button>
</template>
```

### 选项

```ts
const { state } = useAsyncState(promise, initialState, {
  // 在创建时立即执行(默认: true)
  immediate: true,
  // 第一次执行前的延迟,以毫秒为单位(默认: 0)
  delay: 0,
  // 每次执行前将 state 重置为初始值(默认: true)
  resetOnExecute: true,
  // 为 state 使用 shallowRef(默认: true)
  shallow: true,
  // 抛出错误而不是捕获它们(默认: false)
  throwError: false,
  // 当 promise 解析时调用
  onSuccess(data) {
    console.log('Success:', data)
  },
  // 当 promise 拒绝时调用
  onError(error) {
    console.error('Error:', error)
  },
})
```

## 类型声明

```ts
export interface UseAsyncStateReturnBase<
  Data,
  Params extends any[],
  Shallow extends boolean,
> {
  state: Shallow extends true ? Ref<Data> : Ref<UnwrapRef<Data>>
  isReady: Ref<boolean>
  isLoading: Ref<boolean>
  error: Ref<unknown>
  execute: (delay?: number, ...args: Params) => Promise<Data | undefined>
  executeImmediate: (...args: Params) => Promise<Data | undefined>
}
export type UseAsyncStateReturn<
  Data,
  Params extends any[],
  Shallow extends boolean,
> = UseAsyncStateReturnBase<Data, Params, Shallow> &
  PromiseLike<UseAsyncStateReturnBase<Data, Params, Shallow>>
export interface UseAsyncStateOptions<Shallow extends boolean, D = any> {
  /**
   * 当 "immediate" 为 true 时,第一次执行的延迟。以毫秒为单位。
   *
   * @default 0
   */
  delay?: number
  /**
   * 在函数调用后立即执行 promise。
   * 如果有任何延迟,将应用延迟。
   *
   * 当设置为 false 时,您将需要手动执行它。
   *
   * @default true
   */
  immediate?: boolean
  /**
   * 捕获错误时的回调。
   */
  onError?: (e: unknown) => void
  /**
   * 捕获成功时的回调。
   * @param {D} data
   */
  onSuccess?: (data: D) => void
  /**
   * 在执行 promise 之前将 state 设置为 initialState。
   *
   * 当多次调用 execute 函数时(例如,刷新数据)这很有用。当设置为 false 时,当前 state 保持不变,直到 promise 解析。
   *
   * @default true
   */
  resetOnExecute?: boolean
  /**
   * 使用 shallowRef。
   *
   * @default true
   */
  shallow?: Shallow
  /**
   *
   * 执行 execute 函数时抛出错误
   *
   * @default false
   */
  throwError?: boolean
}
/**
 * 响应式异步状态。不会阻塞您的 setup 函数,并且会在 promise 准备好时触发更改。
 *
 * @see https://vueuse.org/useAsyncState
 * @param promise         要解析的 promise / 异步函数
 * @param initialState    第一次评估完成之前使用的初始状态
 * @param options
 */
export declare function useAsyncState<
  Data,
  Params extends any[] = any[],
  Shallow extends boolean = true,
>(
  promise: Promise<Data> | ((...args: Params) => Promise<Data>),
  initialState: MaybeRef<Data>,
  options?: UseAsyncStateOptions<Shallow, Data>,
): UseAsyncStateReturn<Data, Params, Shallow>
```
