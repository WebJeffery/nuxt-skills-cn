---
category: Watch
alias: pausableWatch
---

# watchPausable

可暂停的 watch

::: info
此函数将在未来版本中移除。
:::

::: tip

[可暂停的监听器](https://vuejs.org/api/reactivity-core.html#watch) 已在 Vue [3.5 版本起](https://github.com/vuejs/core/pull/9651) 添加，请改用 `const { stop, pause, resume } = watch(watchSource, callback)`。

:::

## 用法

像普通 `watch` 一样使用，但返回额外的 `pause()` 和 `resume()` 函数来控制。

```ts
import { watchPausable } from '@vueuse/core'
import { nextTick, shallowRef } from 'vue'

const source = shallowRef('foo')

const { stop, pause, resume } = watchPausable(
  source,
  v => console.log(`Changed to ${v}!`),
)

source.value = 'bar'
await nextTick() // Changed to bar!

pause()

source.value = 'foobar'
await nextTick() // (什么都没发生）

resume()

source.value = 'hello'
await nextTick() // Changed to hello!
```

## 类型声明

```ts
export interface WatchPausableReturn extends Pausable {
  stop: WatchStopHandle
}
export type WatchPausableOptions<Immediate> =
  WatchWithFilterOptions<Immediate> & PausableFilterOptions
/** @deprecated 请改用 Vue 内置的 `watch`。此函数将在未来版本中移除。 */
export declare function watchPausable<
  T extends Readonly<MultiWatchSources>,
  Immediate extends Readonly<boolean> = false,
>(
  sources: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, Immediate>>,
  options?: WatchPausableOptions<Immediate>,
): WatchPausableReturn
/** @deprecated 请改用 Vue 内置的 `watch`。此函数将在未来版本中移除。 */
export declare function watchPausable<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T>,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchPausableOptions<Immediate>,
): WatchPausableReturn
/** @deprecated 请改用 Vue 内置的 `watch`。此函数将在未来版本中移除。 */
export declare function watchPausable<
  T extends object,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchPausableOptions<Immediate>,
): WatchPausableReturn
/** @deprecated 请改用 Vue 内置的 `watch`。此函数将在未来版本中移除。 */
export declare const pausableWatch: typeof watchPausable
```