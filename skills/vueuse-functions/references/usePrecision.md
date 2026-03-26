---
category: '@Math'
---

# usePrecision

响应式设置数字的精度。

## 用法

```ts
import { usePrecision } from '@vueuse/math'

const value = ref(3.1415)
const result = usePrecision(value, 2) // 3.14

const ceilResult = usePrecision(value, 2, {
  math: 'ceil'
}) // 3.15

const floorResult = usePrecision(value, 3, {
  math: 'floor'
}) // 3.141
```

## 类型声明

```ts
export interface UsePrecisionOptions {
  /**
   * 用于舍入的方法
   *
   * @default 'round'
   */
  math?: "floor" | "ceil" | "round"
}
/**
 * 响应式设置数字的精度。
 *
 * @see https://vueuse.org/usePrecision
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePrecision(
  value: MaybeRefOrGetter<number>,
  digits: MaybeRefOrGetter<number>,
  options?: MaybeRefOrGetter<UsePrecisionOptions>,
): ComputedRef<number>
```
