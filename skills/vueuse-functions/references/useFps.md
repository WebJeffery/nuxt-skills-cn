---
category: Sensors
---

# useFps

响应式 FPS（每秒帧数）。

## 用法

```ts
import { useFps } from '@vueuse/core'

const fps = useFps()
```

## 类型声明

```ts
export interface UseFpsOptions {
  /**
   * 每 x 帧计算 FPS。
   * @default 10
   */
  every?: number
}
export declare function useFps(options?: UseFpsOptions): ShallowRef<number>
```
