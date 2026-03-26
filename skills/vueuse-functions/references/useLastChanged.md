---
category: State
---

# useLastChanged

记录上次更改的时间戳

## 用法

```ts
import { useLastChanged } from '@vueuse/core'
import { nextTick } from 'vue'

const a = ref(0)
const lastChanged = useLastChanged(a)

a.value = 1

await nextTick()

console.log(lastChanged.value) // 1704709379457
```

默认情况下，更改在下一个刻度上记录（`watch()` 带有 `flush: 'post'`）。如果您想立即记录更改，请将 `flush: 'sync'` 作为第二个参数传递。

```ts
import { useLastChanged } from '@vueuse/core'

const a = ref(0)
const lastChanged = useLastChanged(a, { flush: 'sync' })

a.value = 1

console.log(lastChanged.value) // 1704709379457
```

## Type Declarations

```ts
export interface UseLastChangedOptions<
  Immediate extends boolean,
  InitialValue extends number | null | undefined = undefined,
> extends WatchOptions<Immediate> {
  initialValue?: InitialValue
}
export type UseLastChangedReturn =
  | Readonly<ShallowRef<number | null>>
  | Readonly<ShallowRef<number>>
/**
 * Records the timestamp of the last change
 *
 * @see https://vueuse.org/useLastChanged
 */
export declare function useLastChanged(
  source: WatchSource,
  options?: UseLastChangedOptions<false>,
): Readonly<ShallowRef<number | null>>
export declare function useLastChanged(
  source: WatchSource,
  options: UseLastChangedOptions<true> | UseLastChangedOptions<boolean, number>,
): Readonly<ShallowRef<number>>
```
