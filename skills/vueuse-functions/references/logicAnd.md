---
category: '@Math'
alias: and
related: logicNot, logicOr
---

# logicAnd

ref 的 `AND` 条件。

## 用法

```ts
import { whenever } from '@vueuse/core'
import { logicAnd } from '@vueuse/math'

const a = ref(true)
const b = ref(false)

whenever(logicAnd(a, b), () => {
  console.log('both a and b are now truthy!')
})
```

## 类型声明

```ts
/**
 * Ref 的 `AND` 条件。
 *
 * @see https://vueuse.org/logicAnd
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function logicAnd(
  ...args: MaybeRefOrGetter<any>[]
): ComputedRef<boolean>
/** @deprecated 使用 `logicAnd` 代替 */
export declare const and: typeof logicAnd
```
