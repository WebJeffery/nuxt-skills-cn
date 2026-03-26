---
category: Utilities
---

# useToNumber

响应式 `Number` 转换。

## 用法

```ts
import { useToNumber } from '@vueuse/core'

const value1 = ref('123')
const value2 = ref('456')
const value3 = ref('789')

const result = useToNumber(() => [value1.value, value2.value, value3.value])
console.log(result.value) // [123, 456, 789]
```

## 类型声明

```ts
/**
 * 响应式 `Number` 转换。
 *
 * @see https://vueuse.org/useToNumber
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useToNumber(
  value: MaybeRefOrGetter<unknown>,
  options?: UseToNumberOptions,
): ComputedRef<number>
```
