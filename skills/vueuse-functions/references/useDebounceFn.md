---
category: Utilities
related: useThrottleFn
---

# useDebounceFn

防抖函数执行。

> 防抖是一个过载服务员：如果您一直询问，您的请求将被忽略，直到您停止并给他们一些时间来考虑您的最新询问。

## 用法

```ts
import { useDebounceFn, useEventListener } from '@vueuse/core'

const debouncedFn = useDebounceFn(() => {
  // 做一些事情
}, 1000)

useEventListener(window, 'resize', debouncedFn)
```

您还可以传递第三个参数，最大等待时间，类似于 [lodash debounce](https://lodash.com/docs/4.17.15#debounce)

```ts
import { useDebounceFn, useEventListener } from '@vueuse/core'

// 如果由于重复输入在 5000ms 后没有调用，
// 函数无论如何都会被调用。
const debouncedFn = useDebounceFn(() => {
  // 做一些事情
}, 1000, { maxWait: 5000 })

useEventListener(window, 'resize', debouncedFn)
```

或者，您可以使用 promise 操作获取函数的返回值。

```ts
import { useDebounceFn } from '@vueuse/core'

const debouncedRequest = useDebounceFn(() => 'response', 1000)

debouncedRequest().then((value) => {
  console.log(value) // 'response'
})

// 或使用 async/await
async function doRequest() {
  const value = await debouncedRequest()
  console.log(value) // 'response'
}
```

由于未处理的拒绝错误在开发人员不需要返回值时非常烦人，因此如果函数被取消，promise 将**不会**被拒绝**默认情况下**。您需要指定选项 `rejectOnCancel: true` 来捕获拒绝。

```ts
import { useDebounceFn } from '@vueuse/core'

const debouncedRequest = useDebounceFn(() => 'response', 1000, { rejectOnCancel: true })

debouncedRequest()
  .then((value) => {
    // 做一些事情
  })
  .catch(() => {
    // 取消时做一些事情
  })

// 再次调用它将取消先前的请求并被拒绝
setTimeout(debouncedRequest, 500)
```

## 推荐阅读

- [**防抖 vs 节流**：权威视觉指南](https://kettanaito.com/blog/debounce-vs-throttle)

## 类型声明

```ts
export type UseDebounceFnReturn<T extends FunctionArgs> = PromisifyFn<T>
/**
 * 防抖函数执行。
 *
 * @see https://vueuse.org/useDebounceFn
 * @param  fn          在延迟毫秒后要执行的函数。
 * @param  ms          零或更大的延迟（毫秒）。对于事件回调，100 或 250（甚至更高）左右的值最有用。
 * @param  options     选项
 *
 * @return 一个新的、防抖的函数。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useDebounceFn<T extends FunctionArgs>(
  fn: T,
  ms?: MaybeRefOrGetter<number>,
  options?: DebounceFilterOptions,
): UseDebounceFnReturn<T>
```
