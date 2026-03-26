---
category: Watch
---

# watchAtMost

带有触发次数的 `watch`。

## 用法

与 `watch` 类似，但有一个额外的选项 `count`，用于设置回调函数触发的次数。达到计数后，watch 将自动停止。

```ts
import { watchAtMost } from '@vueuse/core'

watchAtMost(
  source,
  () => { console.log('trigger!') }, // 最多触发 3 次
  {
    count: 3, // 触发次数
  },
)
```

## 类型声明

```ts
export interface WatchAtMostOptions<
  Immediate,
> extends WatchWithFilterOptions<Immediate> {
  count: MaybeRefOrGetter<number>
}
export interface WatchAtMostReturn {
  stop: WatchStopHandle
  pause: () => void
  resume: () => void
  count: ShallowRef<number>
}
export declare function watchAtMost<
  T extends Readonly<MultiWatchSources>,
  Immediate extends Readonly<boolean> = false,
>(
  sources: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, Immediate>>,
  options: WatchAtMostOptions<Immediate>,
): WatchAtMostReturn
export declare function watchAtMost<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  sources: WatchSource<T>,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options: WatchAtMostOptions<Immediate>,
): WatchAtMostReturn
```
