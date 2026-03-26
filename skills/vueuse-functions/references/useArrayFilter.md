---
category: Array
---

# useArrayFilter

响应式 `Array.filter`

## 用法

### 与多个 ref 的数组一起使用

```ts
import { useArrayFilter } from '@vueuse/core'

const item1 = ref(0)
const item2 = ref(2)
const item3 = ref(4)
const item4 = ref(6)
const item5 = ref(8)
const list = [item1, item2, item3, item4, item5]
const result = useArrayFilter(list, i => i % 2 === 0)
// result.value: [0, 2, 4, 6, 8]
item2.value = 1
// result.value: [0, 4, 6, 8]
```

### 与响应式数组一起使用

```ts
import { useArrayFilter } from '@vueuse/core'

const list = ref([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
const result = useArrayFilter(list, i => i % 2 === 0)
// result.value: [0, 2, 4, 6, 8]
list.value.shift()
// result.value: [2, 4, 6, 8]
```

## 类型声明

```ts
export type UseArrayFilterReturn<T = any> = ComputedRef<T[]>
/**
 * 响应式 `Array.filter`
 *
 * @see https://vueuse.org/useArrayFilter
 * @param list - 被调用的数组。
 * @param fn - 为给定 `list` 的每个元素调用的函数。每次 `fn` 执行时,返回的值都会添加到新数组中。
 *
 * @returns 给定数组的部分的浅拷贝,过滤到仅包含通过所提供函数实现的测试的给定数组中的元素。如果没有元素通过测试,将返回一个空数组。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useArrayFilter<T, S extends T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: T[]) => element is S,
): UseArrayFilterReturn<S>
export declare function useArrayFilter<T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: T[]) => unknown,
): UseArrayFilterReturn<T>
```
