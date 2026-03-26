---
category: Array
---

# useArrayFind

响应式 `Array.find`。

## 用法

```ts
import { useArrayFind } from '@vueuse/core'

const list = [ref(1), ref(-1), ref(2)]
const positive = useArrayFind(list, val => val > 0)
// positive.value: 1
```

### 与响应式数组一起使用

```ts
import { useArrayFind } from '@vueuse/core'

const list = reactive([-1, -2])
const positive = useArrayFind(list, val => val > 0)
// positive.value: undefined
list.push(1)
// positive.value: 1
```

## 类型声明

```ts
export type UseArrayFindReturn<T = any> = ComputedRef<T | undefined>
/**
 * 响应式 `Array.find`
 *
 * @see https://vueuse.org/useArrayFind
 * @param list - 被调用的数组。
 * @param fn - 测试每个元素的函数。
 *
 * @returns 数组中满足所提供测试函数的第一个元素。否则,返回 undefined。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useArrayFind<T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: MaybeRefOrGetter<T>[]) => boolean,
): UseArrayFindReturn<T>
```
