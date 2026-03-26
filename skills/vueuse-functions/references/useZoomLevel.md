---
category: '@Electron'
---

# useZoomLevel

响应式 [WebFrame](https://www.electronjs.org/docs/api/web-frame#webframe) 缩放级别。

## 用法

```ts
import { useZoomLevel } from '@vueuse/electron'

// 如果没有显式提供 webFrame，请启用 nodeIntegration
// 参见：https://www.electronjs.org/docs/api/webview-tag#nodeintegration
// Ref 结果将返回
const level = useZoomLevel()
console.log(level.value) // 打印当前缩放级别
level.value = 2 // 更改当前缩放级别
```

立即设置初始缩放级别

```ts
import { useZoomLevel } from '@vueuse/electron'

const level = useZoomLevel(2)
```

传递一个 `ref`，当源 ref 更改时，级别将更新

```ts
import { useZoomLevel } from '@vueuse/electron'
import { shallowRef } from 'vue'

const level = shallowRef(1)

useZoomLevel(level) // 缩放级别将与 ref 匹配

level.value = 2 // 缩放级别将更改
```

## 类型声明

```ts
export declare function useZoomLevel(level: MaybeRef<number>): Ref<number>
export declare function useZoomLevel(
  webFrame: WebFrame,
  level: MaybeRef<number>,
): Ref<number>
export declare function useZoomLevel(webFrame: WebFrame): Ref<number>
export declare function useZoomLevel(): Ref<number>
```
