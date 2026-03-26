---
category: Animation
---

# useTimeoutFn

带有控制的 `setTimeout` 包装器。

## 用法

```ts
import { useTimeoutFn } from '@vueuse/core'

const { isPending, start, stop } = useTimeoutFn(() => {
  /* ... */
}, 3000)
```

## 类型声明

```ts
export interface UseTimeoutFnOptions {
  /**
   * 立即启动计时器
   *
   * @default true
   */
  immediate?: boolean
  /**
   * 在调用 `start` 后立即执行回调
   *
   * @default false
   */
  immediateCallback?: boolean
}
export type UseTimeoutFnReturn<CallbackFn extends AnyFn> = Stoppable<
  Parameters<CallbackFn> | []
>
/**
 * 带有控制的 `setTimeout` 包装器。
 *
 * @param cb
 * @param interval
 * @param options
 */
export declare function useTimeoutFn<CallbackFn extends AnyFn>(
  cb: CallbackFn,
  interval: MaybeRefOrGetter<number>,
  options?: UseTimeoutFnOptions,
): UseTimeoutFnReturn<CallbackFn>
```
