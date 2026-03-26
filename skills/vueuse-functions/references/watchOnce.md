---
category: Watch
---

# watchOnce

使用 `{ once: true }` 监视值的简写。一旦回调触发一次，监视器将停止。有关完整详情，请参阅 [Vue 文档](https://vuejs.org/guide/essentials/watchers.html#once-watchers)。

## 用法

与 `watch` 类似，但使用 `{ once: true }`

```ts
import { watchOnce } from '@vueuse/core'

watchOnce(source, () => {
  // 只触发一次
  console.log('source changed!')
})
```

## 类型声明

```ts
export declare function watchOnce<T extends Readonly<MultiWatchSources>>(
  source: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, true>>,
  options?: Omit<WatchOptions<true>, "once">,
): WatchHandle
export declare function watchOnce<T>(
  source: WatchSource<T>,
  cb: WatchCallback<T, T | undefined>,
  options?: Omit<WatchOptions<true>, "once">,
): WatchHandle
export declare function watchOnce<T extends object>(
  source: T,
  cb: WatchCallback<T, T | undefined>,
  options?: Omit<WatchOptions<true>, "once">,
): WatchHandle
```
