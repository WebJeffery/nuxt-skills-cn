---
category: Elements
---

# useWindowSize

响应式窗口大小

## 用法

```vue
<script setup lang="ts">
import { useWindowSize } from '@vueuse/core'

const { width, height } = useWindowSize()
</script>

<template>
  <div>
    宽度: {{ width }}
    高度: {{ height }}
  </div>
</template>
```

## 组件用法

```vue
<template>
  <UseWindowSize v-slot="{ width, height }">
    宽度: {{ width }}
    高度: {{ height }}
  </UseWindowSize>
</template>
```

## 类型声明

```ts
export interface UseWindowSizeOptions extends ConfigurableWindow {
  initialWidth?: number
  initialHeight?: number
  /**
   * 监听窗口 `orientationchange` 事件
   *
   * @default true
   */
  listenOrientation?: boolean
  /**
   * 宽度和高度是否应包含滚动条
   * 仅在 `type` 为 `'inner'` 时有效
   *
   * @default true
   */
  includeScrollbar?: boolean
  /**
   * 使用 `window.innerWidth` 或 `window.outerWidth` 或 `window.visualViewport`
   * visualViewport 文档来自 MDN(https://developer.mozilla.org/zh-CN/docs/Web/API/VisualViewport)
   * @default 'inner'
   */
  type?: "inner" | "outer" | "visual"
}
export interface UseWindowSizeReturn {
  width: ShallowRef<number>
  height: ShallowRef<number>
}
/**
 * 响应式窗口大小。
 *
 * @see https://vueuse.org/useWindowSize
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useWindowSize(
  options?: UseWindowSizeOptions,
): UseWindowSizeReturn
```
