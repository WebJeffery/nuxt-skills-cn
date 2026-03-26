---
category: Array
---

# useArrayEvery

响应式 `Array.every`

## 用法

### 与多个 ref 的数组一起使用

```ts
import { useArrayEvery } from '@vueuse/core'

const item1 = ref(0)
const item2 = ref(2)
const item3 = ref(4)
const item4 = ref(6)
const item5 = ref(8)
const list = [item1, item2, item3, item4, item5]
const result = useArrayEvery(list, i => i % 2 === 0)
// result.value: true
item1.value = 1
// result.value: false
```

### 与响应式数组一起使用

```ts
import { useArrayEvery } from '@vueuse/core'

const list = ref([0, 2, 4, 6, 8])
const result = useArrayEvery(list, i => i % 2 === 0)
// result.value: true
list.value.push(9)
// result.value: false
```

## 类型声明

```ts
export type UseArrayEveryReturn = ComputedRef<boolean>
/**
 * 响应式 `Array.every`
 *
 * @see https://vueuse.org/useArrayEvery
 * @param list - 被调用的数组。
 * @param fn - 测试每个元素的函数。
 *
 * @returns 如果对于数组中的每个元素,`fn` 函数返回**真值**,则返回 **true**。否则,返回 **false**。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useArrayEvery<T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  fn: (element: T, index: number, array: MaybeRefOrGetter<T>[]) => unknown,
): UseArrayEveryReturn
```
