---
category: '@Math'
alias: not
---

# logicNot

ref 的 `NOT` 条件。

## 用法

```ts
import { whenever } from '@vueuse/core'
import { logicNot } from '@vueuse/math'

const a = ref(true)

whenever(logicNot(a), () => {
  console.log('a is now falsy!')
})
```

## 类型声明

```ts
/**
 * Ref 的 `NOT` 条件。
 *
 * @see https://vueuse.org/logicNot
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function logicNot(v: MaybeRefOrGetter<any>): ComputedRef<boolean>
/** @deprecated 使用 `logicNot` 代替 */
export declare const not: typeof logicNot
```
