---
category: '@Math'
---

# useTrunc

响应式 `Math.trunc`。

## 用法

```ts
import { useTrunc } from '@vueuse/math'

const value1 = ref(0.95)
const value2 = ref(-2.34)
const result1 = useTrunc(value1) // 0
const result2 = useTrunc(value2) // -2
```

## 类型声明

```ts
/**
 * 响应式 `Math.trunc`。
 *
 * @see https://vueuse.org/useTrunc
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useTrunc(
  value: MaybeRefOrGetter<number>,
): ComputedRef<number>
```
