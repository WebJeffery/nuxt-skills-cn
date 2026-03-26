---
category: Watch
---

# watchWithFilter

带有额外 EventFilter 控制的 `watch`。

## 用法

与 `watch` 类似，但提供额外的选项 `eventFilter`，该选项将应用于回调函数。

```ts
import { debounceFilter, watchWithFilter } from '@vueuse/core'

watchWithFilter(
  source,
  () => { console.log('changed!') }, // 回调将以 500ms 防抖方式调用
  {
    eventFilter: debounceFilter(500), // throttledFilter、pausableFilter 或自定义过滤器
  },
)
```

## 类型声明

```ts
export interface WatchWithFilterOptions<Immediate>
  extends WatchOptions<Immediate>, ConfigurableEventFilter {}
export declare function watchWithFilter<
  T extends Readonly<MultiWatchSources>,
  Immediate extends Readonly<boolean> = false,
>(
  sources: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, Immediate>>,
  options?: WatchWithFilterOptions<Immediate>,
): WatchHandle
export declare function watchWithFilter<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T>,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchWithFilterOptions<Immediate>,
): WatchHandle
export declare function watchWithFilter<
  T extends object,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchWithFilterOptions<Immediate>,
): WatchHandle
```