---
category: '@Math'
---

# useFloor

响应式 `Math.floor`。

## 用法

```ts
import { useFloor } from '@vueuse/math'

const value = ref(45.95)
const result = useFloor(value) // 45
```

## 类型声明

```ts
/**
 * 响应式 `Math.floor`
 *
 * @see https://vueuse.org/useFloor
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useFloor(
  value: MaybeRefOrGetter<number>,
): ComputedRef<number>
```
