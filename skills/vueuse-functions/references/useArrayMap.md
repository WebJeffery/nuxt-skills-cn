---
name: useArrayMap
description: 响应式数组的 map 实现
---

# useArrayMap

`useArrayMap` 是 `Array.prototype.map` 的响应式版本。它返回一个计算属性，包含对数组中每个元素调用提供的函数后的结果组成的新数组。

## 用法

```ts
import { useArrayMap } from '@vueuse/core'

const list = ref([1, 2, 3, 4, 5])

const doubled = useArrayMap(list, (item) => item * 2)
console.log(doubled.value) // [2, 4, 6, 8, 10]

const names = useArrayMap(
  list,
  (item) => `Number ${item}`,
)
console.log(names.value) // ['Number 1', 'Number 2', 'Number 3', 'Number 4', 'Number 5']
```

## 类型定义

```ts
export type UseArrayMapReturn<T = any> = ComputedRef<T[]>

export declare function useArrayMap<T, U = T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: T[]) => U,
): UseArrayMapReturn<U>
```

## 参数

- `list`: 要映射的数组，可以是 ref、getter 或普通数组
- `fn`: 映射函数，对数组中的每个元素执行，返回新值

## 返回值

返回一个计算属性，包含对原数组每个元素应用映射函数后的新数组。

<!--
Source references:
- https://vueuse.org/core/useArrayMap/
-->