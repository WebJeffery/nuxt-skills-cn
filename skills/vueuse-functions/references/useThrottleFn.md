---
category: Utilities
related: refThrottled, refDebounced, useDebounceFn
---

# useThrottleFn

节流函数的执行。特别适用于限制事件处理程序的执行频率，例如 resize 和 scroll。

> 节流是一个弹簧，它会抛出球：球飞出后需要一些时间收缩回来，所以除非它准备好了，否则不能再抛出更多的球。

## 用法

```ts
import { useThrottleFn } from '@vueuse/core'

const throttledFn = useThrottleFn(() => {
  // 做一些事情，它最多每秒调用 1 次
}, 1000)

useEventListener(window, 'resize', throttledFn)
```

## 推荐阅读

- [**Debounce vs Throttle**：权威视觉指南](https://kettanaito.com/blog/debounce-vs-throttle)

## 类型声明

```ts
/**
 * 节流函数的执行。特别适用于限制事件处理程序的执行频率，
 * 例如 resize 和 scroll。
 *
 * @param   fn             要在延迟毫秒后执行的函数。`this` 上下文和所有参数都将按原样传递给
 *                                    节流函数执行时的 `callback`。
 * @param   ms             零或更大的延迟（以毫秒为单位）。对于事件回调，100 或 250（甚至更高）左右的值最有用。
 *                                    （默认值：200）
 *
 * @param [trailing] 如果为 true，则在时间结束后再次调用 fn（默认值：false）
 *
 * @param [leading] 如果为 true，则在 ms 超时的前缘调用 fn（默认值：true）
 *
 * @param [rejectOnCancel] 如果为 true，则在取消时拒绝最后一次调用（默认值：false）
 *
 * @return  一个新的、节流的函数。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useThrottleFn<T extends FunctionArgs>(
  fn: T,
  ms?: MaybeRefOrGetter<number>,
  trailing?: boolean,
  leading?: boolean,
  rejectOnCancel?: boolean,
): PromisifyFn<T>
```
