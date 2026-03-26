---
category: Sensors
---

# useMouse

响应式鼠标位置

## 基本用法

```ts twoslash
import { useMouse } from '@vueuse/core'

const { x, y, sourceType } = useMouse()
```

默认情况下启用触摸功能。要仅检测鼠标更改，请将 `touch` 设置为 `false`。
使用 `dragover` 事件在拖动时跟踪鼠标位置。

```ts twoslash
import { useMouse } from '@vueuse/core'
// ---cut---
const { x, y } = useMouse({ touch: false })
```

## 自定义提取器

还可以提供自定义提取器函数以从事件中获取位置。

```ts twoslash
import type { UseMouseEventExtractor } from '@vueuse/core'
import { useMouse, useParentElement } from '@vueuse/core'

const parentEl = useParentElement()

const extractor: UseMouseEventExtractor = event => (
  event instanceof MouseEvent
    ? [event.offsetX, event.offsetY]
    : null
)

const { x, y, sourceType } = useMouse({ target: parentEl, type: extractor })
```

## 组件用法

```vue
<template>
  <UseMouse v-slot="{ x, y }">
    x: {{ x }}
    y: {{ y }}
  </UseMouse>
</template>
```

## 类型声明

```ts
export type UseMouseCoordType = "page" | "client" | "screen" | "movement"
export type UseMouseSourceType = "mouse" | "touch" | null
export type UseMouseEventExtractor = (
  event: MouseEvent | Touch,
) => [x: number, y: number] | null | undefined
export interface UseMouseOptions
  extends ConfigurableWindow, ConfigurableEventFilter {
  /**
   * 基于页面、客户端、屏幕或相对于先前位置的鼠标位置
   *
   * @default 'page'
   */
  type?: UseMouseCoordType | UseMouseEventExtractor
  /**
   * 在 `target` 元素上监听事件
   *
   * @default 'Window'
   */
  target?: MaybeRefOrGetter<Window | EventTarget | null | undefined>
  /**
   * 监听 `touchmove` 事件
   *
   * @default true
   */
  touch?: boolean
  /**
   * 在窗口上监听 `scroll` 事件，仅在类型 `page` 上有效
   *
   * @default true
   */
  scroll?: boolean
  /**
   * 当触发 `touchend` 事件时重置为初始值
   *
   * @default false
   */
  resetOnTouchEnds?: boolean
  /**
   * 初始值
   */
  initialValue?: Position
}
export interface UseMouseReturn {
  x: ShallowRef<number>
  y: ShallowRef<number>
  sourceType: ShallowRef<UseMouseSourceType>
}
/**
 * 响应式鼠标位置。
 *
 * @see https://vueuse.org/useMouse
 * @param options
 */
export declare function useMouse(options?: UseMouseOptions): UseMouseReturn
```
