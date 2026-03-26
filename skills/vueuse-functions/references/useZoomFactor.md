---
category: '@Electron'
---

# useZoomFactor

响应式 [WebFrame](https://www.electronjs.org/docs/api/web-frame#webframe) 缩放因子。

## 用法

```ts
import { useZoomFactor } from '@vueuse/electron'

// 如果没有显式提供 webFrame，请启用 nodeIntegration
// 参见：https://www.electronjs.org/docs/api/webview-tag#nodeintegration
// Ref 结果将返回
const factor = useZoomFactor()
console.log(factor.value) // 打印当前缩放因子
factor.value = 2 // 更改当前缩放因子
```

立即设置初始缩放因子

```ts
import { useZoomFactor } from '@vueuse/electron'

const factor = useZoomFactor(2)
```

传递一个 `ref`，当源 ref 更改时，因子将更新

```ts
import { useZoomFactor } from '@vueuse/electron'
import { shallowRef } from 'vue'

const factor = shallowRef(1)

useZoomFactor(factor) // 缩放因子将与 ref 匹配

factor.value = 2 // 缩放因子将更改
```

## 类型声明

```ts
export declare function useZoomFactor(factor: MaybeRef<number>): Ref<number>
export declare function useZoomFactor(
  webFrame: WebFrame,
  factor: MaybeRef<number>,
): Ref<number>
export declare function useZoomFactor(webFrame: WebFrame): Ref<number>
export declare function useZoomFactor(): Ref<number>
```
