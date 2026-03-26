---
category: Utilities
---

# createEventHook

用于创建事件钩子的工具

## 用法

创建一个使用 `createEventHook` 的函数

```ts
import { createEventHook } from '@vueuse/core'

export function useMyFetch(url) {
  const fetchResult = createEventHook<Response>()
  const fetchError = createEventHook<any>()

  fetch(url)
    .then(result => fetchResult.trigger(result))
    .catch(error => fetchError.trigger(error.message))

  return {
    onResult: fetchResult.on,
    onError: fetchError.on,
  }
}
```

使用一个使用 `createEventHook` 的函数

```vue
<script setup lang="ts">
import { useMyFetch } from './my-fetch-function'

const { onResult, onError } = useMyFetch('my api url')

onResult((result) => {
  console.log(result)
})

onError((error) => {
  console.error(error)
})
</script>
```

## 类型声明

```ts
/**
 * 此函数的源代码灵感来自 vue-apollo 的 `useEventHook` 工具
 * https://github.com/vuejs/vue-apollo/blob/v4/packages/vue-apollo-composable/src/util/useEventHook.ts
 */
type Callback<T> =
  IsAny<T> extends true
    ? (...param: any) => void
    : [T] extends [void]
      ? (...param: unknown[]) => void
      : [T] extends [any[]]
        ? (...param: T) => void
        : (...param: [T, ...unknown[]]) => void
export type EventHookOn<T = any> = (fn: Callback<T>) => {
  off: () => void
}
export type EventHookOff<T = any> = (fn: Callback<T>) => void
export type EventHookTrigger<T = any> = (
  ...param: Parameters<Callback<T>>
) => Promise<unknown[]>
export interface EventHook<T = any> {
  on: EventHookOn<T>
  off: EventHookOff<T>
  trigger: EventHookTrigger<T>
  clear: () => void
}
export type EventHookReturn<T> = EventHook<T>
/**
 * 用于创建事件钩子的工具
 *
 * @see https://vueuse.org/createEventHook
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function createEventHook<T = any>(): EventHookReturn<T>
```
