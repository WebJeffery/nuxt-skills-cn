---
name: useArrayFindLast
description: 响应式数组的 findLast 实现
---

# useArrayFindLast

`useArrayFindLast` 是 `Array.prototype.findLast` 的响应式版本。它返回一个计算属性，包含数组中最后一个满足提供的测试函数的元素的值。如果没有找到匹配的元素，则返回 `undefined`。

## 用法

```ts
import { useArrayFindLast } from '@vueuse/core'

const list = ref([
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' },
])

const item = useArrayFindLast(
  list,
  (item) => item.id > 1,
)

console.log(item.value) // { id: 3, name: 'Charlie' }
```

## 类型定义

```ts
export type UseArrayFindLastReturn<T = any> = ComputedRef<T | undefined>

export declare function useArrayFindLast<T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: MaybeRefOrGetter<T>[]) => boolean,
): UseArrayFindLastReturn<T>
```

## 参数

- `list`: 要搜索的数组，可以是 ref、getter 或普通数组
- `fn`: 测试函数，对数组中的每个元素执行，返回 `true` 表示找到匹配

## 返回值

返回一个计算属性，包含最后一个满足测试函数的元素的值。如果没有找到，返回 `undefined`。

<!--
Source references:
- https://vueuse.org/core/useArrayFindLast/
-->