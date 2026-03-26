---
category: State
related:
  - useRefHistory
  - useThrottledRefHistory
---

# useDebouncedRefHistory

`useRefHistory` 的简写，带有防抖过滤器。

## 用法

此函数在计数器的值开始更改后 1000ms 对计数器进行快照。

```ts
import { useDebouncedRefHistory } from '@vueuse/core'
import { shallowRef } from 'vue'

const counter = shallowRef(0)
const { history, undo, redo } = useDebouncedRefHistory(counter, { deep: true, debounce: 1000 })
```

## 类型声明

```ts
/**
 * [useRefHistory](https://vueuse.org/useRefHistory) 的简写，带有防抖过滤器。
 *
 * @see https://vueuse.org/useDebouncedRefHistory
 * @param source
 * @param options
 */
export declare function useDebouncedRefHistory<Raw, Serialized = Raw>(
  source: Ref<Raw>,
  options?: Omit<UseRefHistoryOptions<Raw, Serialized>, "eventFilter"> & {
    debounce?: MaybeRefOrGetter<number>
  },
): UseRefHistoryReturn<Raw, Serialized>
```
