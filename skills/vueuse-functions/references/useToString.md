---
category: Utilities
---

# useToString

响应式 `String` 转换。

## 用法

```ts
import { useToString } from '@vueuse/core'

const value1 = ref(123)
const value2 = ref(456)
const value3 = ref('789')

const result = useToString(() => [value1.value, value2.value, value3.value])
console.log(result.value) // '123456789'
```

## 类型声明

```ts
/**
 * 响应式 `String` 转换。
 *
 * @see https://vueuse.org/useToString
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useToString(
  value: MaybeRefOrGetter<unknown>,
): ComputedRef<string>
```
