---
category: '@Math'
---

# useCeil

响应式 `Math.ceil`

## 用法

```ts
import { useCeil } from '@vueuse/math'

const value = ref(0.95)
const result = useCeil(value) // 1
```

## 类型声明

```ts
/**
 * 响应式 `Math.ceil`。
 *
 * @see https://vueuse.org/useCeil
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useCeil(
  value: MaybeRefOrGetter<number>,
): ComputedRef<number>
```
