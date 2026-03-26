---
name: useArrayFindIndex
description: 响应式数组的 findIndex 实现
---

# useArrayFindIndex

`useArrayFindIndex` 是 `Array.prototype.findIndex` 的响应式版本。它返回一个计算属性，包含数组中第一个满足提供的测试函数的元素的索引。如果没有找到匹配的元素，则返回 -1。

## 用法

```ts
import { useArrayFindIndex } from '@vueuse/core'

const list = ref([
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' },
])

const index = useArrayFindIndex(
  list,
  (item) => item.id === 2,
)

console.log(index.value) // 1

list.value[0].id = 2
console.log(index.value) // 0
```

## 类型定义

```ts
export type UseArrayFindIndexReturn = ComputedRef<number>

export declare function useArrayFindIndex<T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: MaybeRefOrGetter<T>[]) => unknown,
): UseArrayFindIndexReturn
```

## 参数

- `list`: 要搜索的数组，可以是 ref、getter 或普通数组
- `fn`: 测试函数，对数组中的每个元素执行，返回 truthy 值表示找到匹配

## 返回值

返回一个计算属性，包含第一个满足测试函数的元素的索引。如果没有找到，返回 -1。

<!--
Source references:
- https://vueuse.org/core/useArrayFindIndex/
-->