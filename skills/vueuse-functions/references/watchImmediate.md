---
category: Watch
---

# watchImmediate

使用 `{immediate: true}` 监视值的简写

## 用法

与 `watch` 类似，但使用 `{ immediate: true }`

```ts
import { watchImmediate } from '@vueuse/core'

const obj = ref('vue-use')

// 从某个外部存储/composables 更改值
obj.value = 'VueUse'

watchImmediate(obj, (updated) => {
  console.log(updated) // console.log 将被记录两次
})
```

## 类型声明

```ts
export declare function watchImmediate<T extends Readonly<MultiWatchSources>>(
  source: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, true>>,
  options?: Omit<WatchOptions<true>, "immediate">,
): WatchHandle
export declare function watchImmediate<T>(
  source: WatchSource<T>,
  cb: WatchCallback<T, T | undefined>,
  options?: Omit<WatchOptions<true>, "immediate">,
): WatchHandle
export declare function watchImmediate<T extends object>(
  source: T,
  cb: WatchCallback<T, T | undefined>,
  options?: Omit<WatchOptions<true>, "immediate">,
): WatchHandle
```
