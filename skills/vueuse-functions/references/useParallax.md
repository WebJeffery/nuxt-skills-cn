---
category: Sensors
---

# useParallax

轻松创建视差效果。它使用 `useDeviceOrientation`，如果不支持方向，则回退到 `useMouse`。

## 用法

```vue
<script setup lang="ts">
import { useParallax } from '@vueuse/core'

const container = ref(null)
const { tilt, roll, source } = useParallax(container)
</script>

<template>
  <div ref="container" />
</template>
```

## 类型声明

```ts
export interface UseParallaxOptions extends ConfigurableWindow {
  deviceOrientationTiltAdjust?: (i: number) => number
  deviceOrientationRollAdjust?: (i: number) => number
  mouseTiltAdjust?: (i: number) => number
  mouseRollAdjust?: (i: number) => number
}
export interface UseParallaxReturn {
  /**
   * Roll 值。缩放为 `-0.5 ~ 0.5`
   */
  roll: ComputedRef<number>
  /**
   * Tilt 值。缩放为 `-0.5 ~ 0.5`
   */
  tilt: ComputedRef<number>
  /**
   * 传感器源，可以是 `mouse` 或 `deviceOrientation`
   */
  source: ComputedRef<"deviceOrientation" | "mouse">
}
/**
 * 轻松创建视差效果。它使用 `useDeviceOrientation`，如果不支持方向，则回退到 `useMouse`。
 *
 * @param target
 * @param options
 */
export declare function useParallax(
  target: MaybeElementRef,
  options?: UseParallaxOptions,
): UseParallaxReturn
```
