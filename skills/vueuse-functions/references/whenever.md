---
category: Watch
---

# whenever

监视值为真值的简写。

## 用法

```js
import { useAsyncState, whenever } from '@vueuse/core'

const { state, isReady } = useAsyncState(
  fetch('https://jsonplaceholder.typicode.com/todos/1').then(t => t.json()),
  {},
)

whenever(isReady, () => console.log(state))
```

```ts
import { whenever } from '@vueuse/core'
// ---cut---
// 这个
whenever(ready, () => console.log(state))

// 等价于：
watch(ready, (isReady) => {
  if (isReady)
    console.log(state)
})
```

### 回调函数

与 `watch` 相同，回调将使用 `cb(value, oldValue, onInvalidate)` 调用。

```ts
import { whenever } from '@vueuse/core'
// ---cut---
whenever(height, (current, lastHeight) => {
  if (current > lastHeight)
    console.log(`高度增加了 ${current - lastHeight}`)
})
```

### 计算属性

与 `watch` 相同，可以传递 getter 函数在每次更改时计算。

```ts
import { whenever } from '@vueuse/core'
// ---cut---
// 这个
whenever(
  () => counter.value ===7,
  () => console.log('counter 现在是 7！'),
)
```

### 选项

选项和默认值与 `watch` 相同。

```ts
import { whenever } from '@vueuse/core'
// ---cut---
// 这个
whenever(
  () => counter.value ===7,
  () => console.log('counter 现在是 7！'),
  { flush: 'sync' },
)
```

## 类型声明

```ts
export interface WheneverOptions extends WatchOptions {
  /**
   * 仅在条件满足时触发一次
   *
   * 覆盖 `WatchOptions` 中的 `once` 选项
   *
   * @default false
   */
  once?: boolean
}
/**
 * 监视值为真值的简写
 *
 * @see https://vueuse.org/whenever
 */
export declare function whenever<T>(
  source: WatchSource<T | false | null | undefined>,
  cb: WatchCallback<T>,
  options?: WheneverOptions,
): WatchHandle
```