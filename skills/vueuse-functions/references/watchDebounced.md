---
category: Watch
alias: debouncedWatch
---

# watchDebounced

防抖 watch。回调仅在源停止更改指定持续时间后才会被调用。

## 用法

与 `watch` 类似，但提供额外的选项 `debounce` 和 `maxWait`，这些选项将应用于回调函数。

```ts
import { watchDebounced } from '@vueuse/core'

watchDebounced(
  source,
  () => { console.log('changed!') },
  { debounce: 500, maxWait: 1000 },
)
```

### 选项

| 选项     | 类型                       | 默认值 | 描述                                |
| ---------- | -------------------------- | ------- | ------------------------------------------ |
| `debounce` | `MaybeRefOrGetter<number>` | `0`     | 防抖延迟时间（毫秒，可以是响应式的）     |
| `maxWait`  | `MaybeRefOrGetter<number>` | —       | 强制调用前的最大等待时间 |

所有标准的 `watch` 选项（`deep`、`immediate`、`flush` 等）也都支持。

### 响应式防抖时间

防抖时间可以是响应式的：

```ts
import { watchDebounced } from '@vueuse/core'

const debounceMs = ref(500)

watchDebounced(
  source,
  () => { console.log('changed!') },
  { debounce: debounceMs },
)

// 稍后，更改防抖时间
debounceMs.value = 1000
```

## 工作原理

它本质上是以下代码的简写：

```ts
import { debounceFilter, watchWithFilter } from '@vueuse/core'

watchWithFilter(
  source,
  () => { console.log('changed!') },
  {
    eventFilter: debounceFilter(500, { maxWait: 1000 }),
  },
)
```

## 类型声明

```ts
export interface WatchDebouncedOptions<Immediate>
  extends WatchOptions<Immediate>, DebounceFilterOptions {
  debounce?: MaybeRefOrGetter<number>
}
export declare function watchDebounced<
  T extends Readonly<MultiWatchSources>,
  Immediate extends Readonly<boolean> = false,
>(
  sources: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, Immediate>>,
  options?: WatchDebouncedOptions<Immediate>,
): WatchHandle
export declare function watchDebounced<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T>,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchDebouncedOptions<Immediate>,
): WatchHandle
export declare function watchDebounced<
  T extends object,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchDebouncedOptions<Immediate>,
): WatchHandle
/** @deprecated 请改用 `watchDebounced` */
export declare const debouncedWatch: typeof watchDebounced
```
