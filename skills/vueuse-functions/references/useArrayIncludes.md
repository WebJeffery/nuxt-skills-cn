---
name: useArrayIncludes
description: 响应式数组的 includes 实现
---

# useArrayIncludes

`useArrayIncludes` 是 `Array.prototype.includes` 的响应式版本。它返回一个计算属性，表示数组是否包含某个值。

## 用法

```ts
import { useArrayIncludes } from '@vueuse/core'

const list = ref([1, 2, 3, 4, 5])

const result = useArrayIncludes(list, 3)
console.log(result.value) // true

const result2 = useArrayIncludes(list, 6)
console.log(result2.value) // false
```

### 使用自定义比较器

```ts
const list = ref([
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
])

const result = useArrayIncludes(
  list,
  { id: 2, name: 'Bob' },
  { comparator: (a, b) => a.id === b.id },
)

console.log(result.value) // true
```

### 指定起始索引

```ts
const list = ref([1, 2, 3, 4, 5])

const result = useArrayIncludes(list, 1, { fromIndex: 2 })
console.log(result.value) // false
```

## 类型定义

```ts
export type UseArrayIncludesComparatorFn<T, V> = (
  element: T,
  value: V,
  index: number,
  array: MaybeRefOrGetter<T>[],
) => boolean

export interface UseArrayIncludesOptions<T, V> {
  fromIndex?: number
  comparator?: UseArrayIncludesComparatorFn<T, V> | keyof T
}

export type UseArrayIncludesReturn = ComputedRef<boolean>

export declare function useArrayIncludes<T, V = any>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  value: MaybeRefOrGetter<V>,
  options?: UseArrayIncludesOptions<T, V>,
): UseArrayIncludesReturn
```

## 参数

- `list`: 要搜索的数组，可以是 ref、getter 或普通数组
- `value`: 要查找的值，可以是 ref、getter 或普通值
- `options`:
  - `fromIndex`: 开始搜索的索引位置
  - `comparator`: 自定义比较函数或对象属性名

## 返回值

返回一个计算属性，表示数组是否包含指定的值。

<!--
Source references:
- https://vueuse.org/core/useArrayIncludes/
-->