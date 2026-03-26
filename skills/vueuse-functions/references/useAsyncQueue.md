---
category: Utilities
---

# useAsyncQueue

按顺序执行每个异步任务,并将当前任务结果传递给下一个任务。

## 用法

```ts
import { useAsyncQueue } from '@vueuse/core'

function p1() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(1000)
    }, 10)
  })
}

function p2(result: number) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(1000 + result)
    }, 20)
  })
}

const { activeIndex, result } = useAsyncQueue([p1, p2])

console.log(activeIndex.value) // 当前待处理任务的索引

console.log(result) // 任务结果
```

### 结果状态

result 数组中的每个任务都有一个 `state` 和 `data` 属性:

```ts
interface UseAsyncQueueResult<T> {
  state: 'aborted' | 'fulfilled' | 'pending' | 'rejected'
  data: T | null
}
```

### 失败时中断

默认情况下,如果任务失败,则不会执行后续任务。设置 `interrupt: false` 以在失败后继续执行。

```ts
const { result } = useAsyncQueue([p1, p2], {
  interrupt: false, // 即使 p1 失败也继续
})
```

### 回调

```ts
const { result } = useAsyncQueue([p1, p2], {
  onError() {
    console.log('任务失败')
  },
  onFinished() {
    console.log('所有任务完成(或中断)')
  },
})
```

### 中止信号

您可以传递 `AbortSignal` 来取消队列执行。

```ts
const controller = new AbortController()

const { result } = useAsyncQueue([p1, p2], {
  signal: controller.signal,
})

// 稍后,中止队列
controller.abort()
```

## 类型声明

```ts
export type UseAsyncQueueTask<T> = (...args: any[]) => T | Promise<T>
type MapQueueTask<T extends any[]> = {
  [K in keyof T]: UseAsyncQueueTask<T[K]>
}
export interface UseAsyncQueueResult<T> {
  state: "aborted" | "fulfilled" | "pending" | "rejected"
  data: T | null
}
export interface UseAsyncQueueReturn<T> {
  activeIndex: ShallowRef<number>
  result: T
}
export interface UseAsyncQueueOptions {
  /**
   * 当前任务失败时中断任务。
   *
   * @default true
   */
  interrupt?: boolean
  /**
   * 任务失败时触发它。
   *
   */
  onError?: () => void
  /**
   * 任务结束时触发它。
   *
   */
  onFinished?: () => void
  /**
   * 可用于中止任务的 AbortSignal。
   */
  signal?: AbortSignal
}
/**
 * 异步队列任务控制器。
 *
 * @see https://vueuse.org/useAsyncQueue
 * @param tasks
 * @param options
 */
export declare function useAsyncQueue<T extends any[], S = MapQueueTask<T>>(
  tasks: S & Array<UseAsyncQueueTask<any>>,
  options?: UseAsyncQueueOptions,
): UseAsyncQueueReturn<{
  [P in keyof T]: UseAsyncQueueResult<T[P]>
}>
```
