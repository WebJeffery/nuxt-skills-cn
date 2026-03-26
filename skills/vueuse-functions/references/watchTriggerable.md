---
category: Watch
---

# watchTriggerable

可以手动触发的 watch

## 用法

一个 `watch` 包装器，支持手动触发 `WatchCallback`，它返回一个额外的 `trigger` 来立即执行 `WatchCallback`。

```ts
import { watchTriggerable } from '@vueuse/core'
import { nextTick, shallowRef } from 'vue'

const source = shallowRef(0)

const { trigger, ignoreUpdates } = watchTriggerable(
  source,
  v => console.log(`Changed to ${v}!`),
)

source.value = 'bar'
await nextTick() // 日志：Changed to bar!

// 通过 `trigger` 执行 WatchCallback 不需要等待
trigger() // 日志：Changed to bar!
```

### `onCleanup`

当你想要手动调用一个使用 onCleanup 参数的 `watch`；简单地取出 `WatchCallback` 并调用它并不能很容易实现 `onCleanup` 参数。

使用 `watchTriggerable` 将解决这个问题。

```ts
import { watchTriggerable } from '@vueuse/core'
import { shallowRef } from 'vue'

const source = shallowRef(0)

const { trigger } = watchTriggerable(
  source,
  async (v, _, onCleanup) => {
    let canceled = false
    onCleanup(() => canceled = true)

    await new Promise(resolve => setTimeout(resolve, 500))
    if (canceled)
      return

    console.log(`The value is "${v}"\n`)
  },
)

source.value = 1 // 无日志
await trigger() // 日志（500ms 后）：The value is "1"
```

## 类型声明

```ts
export interface WatchTriggerableReturn<
  FnReturnT = void,
> extends WatchIgnorableReturn {
  /** 立即执行 `WatchCallback` */
  trigger: () => FnReturnT
}
type OnCleanup = (cleanupFn: () => void) => void
export type WatchTriggerableCallback<V = any, OV = any, R = void> = (
  value: V,
  oldValue: OV,
  onCleanup: OnCleanup,
) => R
export declare function watchTriggerable<
  T extends Readonly<MultiWatchSources>,
  FnReturnT,
>(
  sources: [...T],
  cb: WatchTriggerableCallback<
    MapSources<T>,
    MapOldSources<T, true>,
    FnReturnT
  >,
  options?: WatchWithFilterOptions<boolean>,
): WatchTriggerableReturn<FnReturnT>
export declare function watchTriggerable<T, FnReturnT>(
  source: WatchSource<T>,
  cb: WatchTriggerableCallback<T, T | undefined, FnReturnT>,
  options?: WatchWithFilterOptions<boolean>,
): WatchTriggerableReturn<FnReturnT>
export declare function watchTriggerable<T extends object, FnReturnT>(
  source: T,
  cb: WatchTriggerableCallback<T, T | undefined, FnReturnT>,
  options?: WatchWithFilterOptions<boolean>,
): WatchTriggerableReturn<FnReturnT>
```