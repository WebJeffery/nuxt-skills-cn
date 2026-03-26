---
category: State
related: useDebouncedRefHistory, useRefHistory
---

# useThrottledRefHistory

`useRefHistory` 带有节流过滤器的简写。

## 用法

此函数在计数器的值更改后立即拍摄第一个快照，并在 1000 毫秒的延迟后拍摄第二个快照。

```ts
import { useThrottledRefHistory } from '@vueuse/core'
import { shallowRef } from 'vue'

const counter = shallowRef(0)
const { history, undo, redo } = useThrottledRefHistory(counter, { deep: true, throttle: 1000 })
```

## 类型声明

```ts
export type UseThrottledRefHistoryOptions<Raw, Serialized = Raw> = Omit<
  UseRefHistoryOptions<Raw, Serialized>,
  "eventFilter"
> & {
  throttle?: MaybeRef<number>
  trailing?: boolean
}
export type UseThrottledRefHistoryReturn<
  Raw,
  Serialized = Raw,
> = UseRefHistoryReturn<Raw, Serialized>
/**
 * [useRefHistory](https://vueuse.org/useRefHistory) 带有节流过滤器的简写。
 *
 * @see https://vueuse.org/useThrottledRefHistory
 * @param source
 * @param options
 */
export declare function useThrottledRefHistory<Raw, Serialized = Raw>(
  source: Ref<Raw>,
  options?: UseThrottledRefHistoryOptions<Raw, Serialized>,
): UseThrottledRefHistoryReturn<Raw, Serialized>
```
