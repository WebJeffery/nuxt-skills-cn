---
category: '@Math'
---

# useRound

响应式 `Math.round`。

## 用法

```ts
import { useRound } from '@vueuse/math'

const value = ref(20.49)
const result = useRound(value) // 20
```

## Type Declarations

```ts
/**
 * Reactive `Math.round`.
 *
 * @see https://vueuse.org/useRound
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useRound(
  value: MaybeRefOrGetter<number>,
): ComputedRef<number>
```
