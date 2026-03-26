---
category: Utilities
---

# useCounter

带有实用函数的基本计数器。

## 基本用法

```ts
import { useCounter } from '@vueuse/core'

const { count, inc, dec, set, reset } = useCounter()
```

## 使用选项

```ts
import { useCounter } from '@vueuse/core'

const { count, inc, dec, set, reset } = useCounter(1, { min: 0, max: 16 })
```

## 类型声明

```ts
export interface UseCounterOptions {
  min?: number
  max?: number
}
export interface UseCounterReturn {
  /**
   * 计数器的当前值。
   */
  readonly count: Readonly<Ref<number>>
  /**
   * 增加计数器。
   *
   * @param {number} [delta=1] 要增加的数字。
   */
  inc: (delta?: number) => void
  /**
   * 减少计数器。
   *
   * @param {number} [delta=1] 要减少的数字。
   */
  dec: (delta?: number) => void
  /**
   * 获取计数器的当前值。
   */
  get: () => number
  /**
   * 将计数器设置为新值。
   *
   * @param val 计数器的新值。
   */
  set: (val: number) => void
  /**
   * 将计数器重置为初始值。
   */
  reset: (val?: number) => number
}
/**
 * 带有实用函数的基本计数器。
 *
 * @see https://vueuse.org/useCounter
 * @param [initialValue]
 * @param options
 */
export declare function useCounter(
  initialValue?: MaybeRef<number>,
  options?: UseCounterOptions,
): {
  count: Readonly<
    | Ref<number, number>
    | ShallowRef<number, number>
    | WritableComputedRef<number, number>
  >
  inc: (delta?: number) => number
  dec: (delta?: number) => number
  get: () => number
  set: (val: number) => number
  reset: (val?: number) => number
}
```
