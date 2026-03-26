---
category: Elements
---

# useMouseInElement

与元素相关的响应式鼠标位置

## 用法

```vue
<script setup lang="ts">
import { useMouseInElement } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const target = useTemplateRef('target')

const { x, y, isOutside } = useMouseInElement(target)
</script>

<template>
  <div ref="target">
    <h1>Hello world</h1>
  </div>
</template>
```

## 组件用法

```vue
<template>
  <UseMouseInElement v-slot="{ elementX, elementY, isOutside }">
    x: {{ elementX }}
    y: {{ elementY }}
    Is Outside: {{ isOutside }}
  </UseMouseInElement>
</template>
```

## 指令用法

```vue
<script setup lang="ts">
import { vMouseInElement } from '@vueuse/components'
import { UseMouseSourceType } from '@vueuse/core'

interface MouseInElementType {
  x: number
  y: number
  sourceType: UseMouseSourceType
  elementX: number
  elementY: number
  elementPositionX: number
  elementPositionY: number
  elementHeight: number
  elementWidth: number
  isOutside: boolean
}

const options = {
  handleOutside: true
}
function onMouseInElement({ x, y, sourceType, elementX, elementY, elementPositionX, elementPositionY, elementHeight, elementWidth, isOutside }: MouseInElementType) {
  console.log(x, y, sourceType, elementX, elementY, elementPositionX, elementPositionY, elementHeight, elementWidth, isOutside)
}
</script>

<template>
  <textarea v-mouse-in-element="onMouseInElement" />
  <!-- 带选项 -->
  <textarea v-mouse-in-element="[onMouseInElement, options]" />
</template>
```

## 类型声明

```ts
export interface MouseInElementOptions extends UseMouseOptions {
  /**
   * 当光标在目标元素之外时是否处理鼠标事件。
   * 启用后，即使鼠标在元素边界之外，也会继续跟踪鼠标位置。
   *
   * @default true
   */
  handleOutside?: boolean
  /**
   * 监听窗口调整大小事件
   *
   * @default true
   */
  windowScroll?: boolean
  /**
   * 监听窗口滚动事件
   *
   * @default true
   */
  windowResize?: boolean
}
export interface UseMouseInElementReturn extends UseMouseReturn {
  elementX: ShallowRef<number>
  elementY: ShallowRef<number>
  elementPositionX: ShallowRef<number>
  elementPositionY: ShallowRef<number>
  elementHeight: ShallowRef<number>
  elementWidth: ShallowRef<number>
  isOutside: ShallowRef<boolean>
  stop: () => void
}
/**
 * 与元素相关的响应式鼠标位置。
 *
 * @see https://vueuse.org/useMouseInElement
 * @param target
 * @param options
 */
export declare function useMouseInElement(
  target?: MaybeElementRef,
  options?: MouseInElementOptions,
): {
  x: ShallowRef<number>
  y: ShallowRef<number>
  sourceType: ShallowRef<UseMouseSourceType>
  elementX: ShallowRef<number, number>
  elementY: ShallowRef<number, number>
  elementPositionX: ShallowRef<number, number>
  elementPositionY: ShallowRef<number, number>
  elementHeight: ShallowRef<number, number>
  elementWidth: ShallowRef<number, number>
  isOutside: ShallowRef<boolean, boolean>
  stop: () => void
}
```
