---
category: Reactivity
alias: useThrottle, throttledRef
---

# refThrottled

节流 ref 值的更改。

## 用法

```ts
import { refThrottled } from '@vueuse/core'
import { shallowRef } from 'vue'

const input = shallowRef('')
const throttled = refThrottled(input, 1000)
```

对象 ref 的示例。

```js
import { refThrottled } from '@vueuse/core'
import { shallowRef } from 'vue'

const data = shallowRef({
  count: 0,
  name: 'foo',
})
const throttled = refThrottled(data, 1000)

data.value = { count:1, name: 'foo' }
console.log(throttled.value) // { count: 1, name: 'foo' } (立即)

data.value = { count: 2, name: 'bar' }
data.value = { count: 3, name: 'baz' }
data.value = { count: 4, name: 'qux' }
console.log(throttled.value) // { count: 1, name: 'foo' } (仍然是第一个值)

// 1000ms 后,下一个更改将被应用
await sleep(1100)
data.value = { count: 5, name: 'final' }
await nextTick()
console.log(throttled.value) // { count: 5, name: 'final' } (已更新)
```

### Trailing

如果您不想观察尾随更改,设置第 3 个参数 `false`(默认为 `true`):

```ts
import { refThrottled } from '@vueuse/core'
import { shallowRef } from 'vue'

const input = shallowRef('')
const throttled = refThrottled(input, 1000, false)
```

### Leading

允许回调立即调用(在 `ms` 超时的前缘)。如果您不想要此行为,设置第 4 个参数 `false`(默认为 `true`):

```ts
import { refThrottled } from '@vueuse/core'
import { shallowRef } from 'vue'

const input = shallowRef('')
const throttled = refThrottled(input, 1000, undefined, false)
```

## 推荐阅读

- [防抖 vs 节流: 决定性视觉指南](https://kettanaito.com/blog/debounce-vs-throttle)
- [通过示例解释防抖和节流](https://css-tricks.com/debouncing-throttling-explained-examples/)

## 类型声明

```ts
export type RefThrottledReturn<T = any> = Ref<T>
/**
 * 节流函数的执行。特别适用于限制事件处理程序的执行频率,如 resize 和 scroll。
 *
 * @param value 要用节流效果观察的 Ref 值
 * @param delay  以毫秒为单位的零或更大的延迟。对于事件回调,大约 100 或 250(或甚至更高)的值最有用。
 * @param trailing 如果为 true,则在延迟时间结束后再次更新值
 * @param leading 如果为 true,则在 ms 超时的前缘更新值
 */
export declare function refThrottled<T = any>(
  value: Ref<T>,
  delay?: number,
  trailing?: boolean,
  leading?: boolean,
): RefThrottledReturn<T>
/** @deprecated 使用 `refThrottled` 代替 */
export declare const throttledRef: typeof refThrottled
/** @deprecated 使用 `refThrottled` 代替 */
export declare const useThrottle: typeof refThrottled
```
