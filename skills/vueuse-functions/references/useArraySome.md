---
category: Array
---

# useArraySome

响应式 `Array.some`

## 用法

### 与多个 ref 的数组一起使用

```ts
import { useArraySome } from '@vueuse/core'

const item1 = ref(0)
const item2 = ref(2)
const item3 = ref(4)
const item4 = ref(6)
const item5 = ref(8)
const list = [item1, item2, item3, item4, item5]
const result = useArraySome(list, i => i > 10)
// result.value: false
item1.value = 11
// result.value: true
```

### 与响应式数组一起使用

```ts
import { useArraySome } from '@vueuse/core'

const list = ref([0, 2, 4, 6, 8])
const result = useArraySome(list, i => i > 10)
// result.value: false
list.value.push(11)
// result.value: true
```

## 类型声明

```ts
export type UseArraySomeReturn = ComputedRef<boolean>
/**
 * 响应式 `Array.some`
 *
 * @see https://vueuse.org/useArraySome
 * @param list - 被调用的数组。
 * @param fn - 测试每个元素的函数。
 *
 * @returns 如果对于数组中的任何元素,`fn` 函数返回**真值**,则返回 **true**。否则,返回 **false**。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useArraySome<T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: MaybeRefOrGetter<T>[]) => unknown,
): UseArraySomeReturn
```
