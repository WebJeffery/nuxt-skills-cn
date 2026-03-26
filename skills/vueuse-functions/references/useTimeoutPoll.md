---
category: Utilities
---

# useTimeoutPoll

使用超时来轮询某些内容。它将在最后一个任务完成后触发回调。

## 用法

```ts
import { useTimeoutPoll } from '@vueuse/core'

const count = ref(0)

async function fetchData() {
  await new Promise(resolve => setTimeout(resolve, 1000))
  count.value++
}

// 仅在上一次获取完成后触发
const { isActive, pause, resume } = useTimeoutPoll(fetchData, 1000)
```

## 类型声明

```ts
export interface UseTimeoutPollOptions {
  /**
   * 立即启动计时器
   *
   * @default true
   */
  immediate?: boolean
  /**
   * 在调用 `resume` 后立即执行回调
   *
   * @default false
   */
  immediateCallback?: boolean
}
export declare function useTimeoutPoll(
  fn: () => Awaitable<void>,
  interval: MaybeRefOrGetter<number>,
  options?: UseTimeoutFnOptions,
): Pausable
```
