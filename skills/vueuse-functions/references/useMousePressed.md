---
category: Sensors
---

# useMousePressed

响应式鼠标按下状态。由目标元素上的 `mousedown` `touchstart` 触发，并由窗口上的 `mouseup` `mouseleave` `touchend` `touchcancel` 释放。

## 基本用法

```ts
import { useMousePressed } from '@vueuse/core'

const { pressed } = useMousePressed()
```

默认情况下启用触摸功能。要使其仅检测鼠标更改，请将 `touch` 设置为 `false`

```ts
import { useMousePressed } from '@vueuse/core'
// ---cut---
const { pressed } = useMousePressed({ touch: false })
```

要仅在特定元素上捕获 `mousedown` 和 `touchstart`，可以通过传递元素的 ref 来指定 `target`。

```vue
<script setup lang="ts">
import { useMousePressed } from '@vueuse/core'
// ---cut---
import { useTemplateRef } from 'vue'

const el = useTemplateRef('el')

const { pressed } = useMousePressed({ target: el })
</script>

<template>
  <div ref="el">
    仅点击此元素才会触发更新。
  </div>
</template>
```

## 组件用法

```vue
<template>
  <UseMousePressed v-slot="{ pressed }">
    Is Pressed: {{ pressed }}
  </UseMousePressed>
</template>
```

## 类型声明

```ts
export interface UseMousePressedOptions extends ConfigurableWindow {
  /**
   * 监听 `touchstart` `touchend` 事件
   *
   * @default true
   */
  touch?: boolean
  /**
   * 监听 `dragstart` `drop` 和 `dragend` 事件
   *
   * @default true
   */
  drag?: boolean
  /**
   * 使用 `capture` 选项设置为 `true` 添加事件监听器
   * （参见 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#capture))
   *
   * @default false
   */
  capture?: boolean
  /**
   * 初始值
   *
   * @default false
   */
  initialValue?: boolean
  /**
   * 要捕获点击的元素目标
   */
  target?: MaybeComputedElementRef
  /**
   * 当鼠标按下时调用的回调
   *
   * @param event
   */
  onPressed?: (event: MouseEvent | TouchEvent | DragEvent) => void
  /**
   * 当鼠标释放时调用的回调
   *
   * @param event
   */
  onReleased?: (event: MouseEvent | TouchEvent | DragEvent) => void
}
/** @deprecated 请改用 {@link UseMousePressedOptions} */
export type MousePressedOptions = UseMousePressedOptions
export interface UseMousePressedReturn {
  pressed: ShallowRef<boolean>
  sourceType: ShallowRef<UseMouseSourceType>
}
/**
 * 响应式鼠标按下状态。
 *
 * @see https://vueuse.org/useMousePressed
 * @param options
 */
export declare function useMousePressed(
  options?: UseMousePressedOptions,
): UseMousePressedReturn
```
