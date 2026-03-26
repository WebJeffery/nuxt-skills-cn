---
category: Watch
---

# watchThrottled

节流 watch。回调最多在指定持续时间内调用一次。

## 用法

与 `watch` 类似，但提供额外的选项 `throttle`、`trailing` 和 `leading`，这些选项将应用于回调函数。

```ts
import { watchThrottled } from '@vueuse/core'

watchThrottled(
  source,
  () => { console.log('changed!') },
  { throttle: 500 },
)
```

### 选项

| 选项     | 类型                       | 默认值 | 描述                               |
| ---------- | -------------------------- | ------- | ----------------------------------------- |
| `throttle` | `MaybeRefOrGetter<number>` | `0`     | 节流间隔（毫秒，可以是响应式的） |
| `trailing` | `boolean`                  | `true`  | 在尾部边缘调用               |
| `leading`  | `boolean`                  | `true`  | 在头部边缘调用                |

所有标准的 `watch` 选项（`deep`、`immediate`、`flush` 等）也都支持。

### 头部和尾部

控制何时调用回调：

```ts
import { watchThrottled } from '@vueuse/core'

// 仅在每个节流周期的开始时调用
watchThrottled(source, callback, {
  throttle: 500,
  leading: true,
  trailing: false,
})

// 仅在每个节流周期的结束时调用
watchThrottled(source, callback, {
  throttle: 500,
  leading: false,
  trailing: true,
})
```

## 工作原理

它本质上是以下代码的简写：

```ts
import { throttleFilter, watchWithFilter } from '@vueuse/core'

watchWithFilter(
  source,
  () => { console.log('changed!') },
  {
    eventFilter: throttleFilter(500),
  },
)
```

## 类型声明

```ts
export interface WatchThrottledOptions<
  Immediate,
> extends WatchOptions<Immediate> {
  throttle?: MaybeRefOrGetter<number>
  trailing?: boolean
  leading?: boolean
}
export declare function watchThrottled<
  T extends Readonly<MultiWatchSources>,
  Immediate extends Readonly<boolean> = false,
>(
  sources: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, Immediate>>,
  options?: WatchThrottledOptions<Immediate>,
): WatchHandle
export declare function watchThrottled<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T>,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchThrottledOptions<Immediate>,
): WatchHandle
export declare function watchThrottled<
  T extends object,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchThrottledOptions<Immediate>,
): WatchHandle
/** @deprecated 请改用 `watchThrottled` */
export declare const throttledWatch: typeof watchThrottled
```