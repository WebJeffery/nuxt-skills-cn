---
category: Array
---

# useArrayReduce

响应式 `Array.reduce`。

## 用法

```ts
import { useArrayReduce } from '@vueuse/core'

const sum = useArrayReduce([ref(1), ref(2), ref(3)], (sum, val) => sum + val)
// sum.value: 6
```

### 与响应式数组一起使用

```ts
import { useArrayReduce } from '@vueuse/core'

const list = reactive([1, 2])
const sum = useArrayReduce(list, (sum, val) => sum + val)

list.push(3)
// sum.value: 6
```

### 使用初始值

```ts
import { useArrayReduce } from '@vueuse/core'

const list = reactive([{ num: 1 }, { num: 2 }])
const sum = useArrayReduce(list, (sum, val) => sum + val.num, 0)
// sum.value: 3
```

## 类型声明

```ts
export type UseArrayReducer<PV, CV, R> = (
  previousValue: PV,
  currentValue: CV,
  currentIndex: number,
) => R
export type UseArrayReduceReturn<T = any> = ComputedRef<T>
/**
 * 响应式 `Array.reduce`
 *
 * @see https://vueuse.org/useArrayReduce
 * @param list - 被调用的数组。
 * @param reducer - 一个 "reducer" 函数。
 *
 * @returns 在整个数组上运行 "reducer" 回调函数到完成所产生的值。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useArrayReduce<T>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  reducer: UseArrayReducer<T, T, T>,
): UseArrayReduceReturn<T>
/**
 * 响应式 `Array.reduce`
 *
 * @see https://vueuse.org/useArrayReduce
 * @param list - 被调用的数组。
 * @param reducer - 一个 "reducer" 函数。
 * @param initialValue - 在第一次调用回调时初始化的值。
 *
 * @returns 在整个数组上运行 "reducer" 回调函数到完成所产生的值。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useArrayReduce<T, U>(
  list: MaybeRefOrGetter<MaybeRefOrGetter<T>[]>,
  reducer: UseArrayReducer<U, T, U>,
  initialValue: MaybeRefOrGetter<U>,
): UseArrayReduceReturn<U>
```
