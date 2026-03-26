---
category: '@Math'
alias: or
related: logicAnd, logicNot
---

# logicOr

ref 的 `OR` 条件。

## 用法

```ts
import { whenever } from '@vueuse/core'
import { logicOr } from '@vueuse/math'

const a = ref(true)
const b = ref(false)

whenever(logicOr(a, b), () => {
  console.log('either a or b is truthy!')
})
```

## 类型声明

```ts
/**
 * Ref 的 `OR` 条件。
 *
 * @see https://vueuse.org/logicOr
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function logicOr(
  ...args: MaybeRefOrGetter<any>[]
): ComputedRef<boolean>
/** @deprecated 使用 `logicOr` 代替 */
export declare const or: typeof logicOr
```
