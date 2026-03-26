---
category: '@Math'
---

# useAbs

响应式 `Math.abs`。

## 用法

```ts
import { useAbs } from '@vueuse/math'

const value = ref(-23)
const absValue = useAbs(value) // Ref<23>
```

## 类型声明

```ts
/**
 * 响应式 `Math.abs`。
 *
 * @see https://vueuse.org/useAbs
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useAbs(
  value: MaybeRefOrGetter<number>,
): ComputedRef<number>
```
