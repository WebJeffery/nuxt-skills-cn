---
category: Utilities
related: reactify
---

# createUnrefFn

使普通函数接受 ref 和原始值作为参数。
返回与未转换函数相同的值,具有适当的类型。

::: tip
确保您使用正确的工具。在某些情况下,您希望在参数的每次更改时评估函数,使用 `reactify` 可能更相关。
:::

## 用法

```ts
import { createUnrefFn } from '@vueuse/core'
import { shallowRef } from 'vue'

const url = shallowRef('https://httpbin.org/post')
const data = shallowRef({ foo: 'bar' })

function post(url, data) {
  return fetch(url, { data })
}
const unrefPost = createUnrefFn(post)

post(url, data) /* ❌ 将抛出错误,因为参数是 refs */
unrefPost(url, data) /* ✔️ 将工作,因为参数将自动 unref */
```

## 类型声明

```ts
export type UnrefFn<T> = T extends (...args: infer A) => infer R
  ? (
      ...args: {
        [K in keyof A]: MaybeRef<A[K]>
      }
    ) => R
  : never
/**
 * 使普通函数接受 ref 和原始值作为参数。
 * 返回与未转换函数相同的值,具有适当的类型。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function createUnrefFn<T extends Function>(fn: T): UnrefFn<T>
```
