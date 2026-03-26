---
category: Watch
alias: ignorableWatch
---

# watchIgnorable

可忽略的 watch

## 用法

扩展的 `watch`，返回额外的 `ignoreUpdates(updater)` 和 `ignorePrevAsyncUpdates()` 来忽略对源的特定更新。

```ts
import { watchIgnorable } from '@vueuse/core'
import { nextTick, shallowRef } from 'vue'

const source = shallowRef('foo')

const { stop, ignoreUpdates } = watchIgnorable(
  source,
  v => console.log(`Changed to ${v}!`),
)

source.value = 'bar'
await nextTick() // 日志：Changed to bar!

ignoreUpdates(() => {
  source.value = 'foobar'
})
await nextTick() // (什么都没发生）

source.value = 'hello'
await nextTick() // 日志：Changed to hello!

ignoreUpdates(() => {
  source.value = 'ignored'
})
source.value = 'logged'

await nextTick() // 日志：Changed to logged!
```

## WatchOptionFlush 时序

`watchIgnorable` 接受与 `watch` 相同的选项并使用相同的默认值。因此，默认情况下，该组合式函数使用 `flush: 'pre'` 工作。

## `ignorePrevAsyncUpdates`

此功能仅适用于异步刷新 `'pre'` 和 `'post'`。如果使用 `flush: 'sync'`，`ignorePrevAsyncUpdates()` 是一个空操作，因为 watch 会在每次更新源后立即触发。它仍然为同步刷新提供，以便代码可以更通用。

```ts
import { watchIgnorable } from '@vueuse/core'
import { nextTick, shallowRef } from 'vue'

const source = shallowRef('foo')

const { ignorePrevAsyncUpdates } = watchIgnorable(
  source,
  v => console.log(`Changed to ${v}!`),
)

source.value = 'bar'
await nextTick() // 日志：Changed to bar!

source.value = 'good'
source.value = 'by'
ignorePrevAsyncUpdates()

await nextTick() // (什么都没发生）

source.value = 'prev'
ignorePrevAsyncUpdates()
source.value = 'after'

await nextTick() // 日志：Changed to after!
```

## 推荐阅读

- [Ignorable Watch](https://patak.dev/vue/ignorable-watch.html) - 作者 [@patak-dev](https://github.com/patak-dev)

## 类型声明

```ts
export type IgnoredUpdater = (updater: () => void) => void
export type IgnoredPrevAsyncUpdates = () => void
export interface WatchIgnorableReturn {
  ignoreUpdates: IgnoredUpdater
  ignorePrevAsyncUpdates: IgnoredPrevAsyncUpdates
  stop: WatchStopHandle
}
export declare function watchIgnorable<
  T extends Readonly<MultiWatchSources>,
  Immediate extends Readonly<boolean> = false,
>(
  sources: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, Immediate>>,
  options?: WatchWithFilterOptions<Immediate>,
): WatchIgnorableReturn
export declare function watchIgnorable<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T>,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchWithFilterOptions<Immediate>,
): WatchIgnorableReturn
export declare function watchIgnorable<
  T extends object,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchWithFilterOptions<Immediate>,
): WatchIgnorableReturn
/** @deprecated 请改用 `watchIgnorable` */
export declare const ignorableWatch: typeof watchIgnorable
```
