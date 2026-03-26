---
category: Elements
---

# useDraggable

使元素可拖动。

## 用法

```vue
<script setup lang="ts">
import { useDraggable } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const el = useTemplateRef('el')

// `style` 将是 `left: ?px; top: ?px;` 的辅助计算属性
const { x, y, style } = useDraggable(el, {
  initialValue: { x: 40, y: 40 },
})
</script>

<template>
  <div ref="el" :style="style" style="position: fixed">
    拖动我！我在 {{ x }}, {{ y }}
  </div>
</template>
```

### 返回值

| 属性     | 类型                   | 描述                             |
| ------------ | ---------------------- | --------------------------------------- |
| `x`          | `Ref<number>`          | 当前 x 位置                      |
| `y`          | `Ref<number>`          | 当前 y 位置                      |
| `position`   | `Ref<{x, y}>`          | 当前位置对象                 |
| `isDragging` | `ComputedRef<boolean>` | 是否正在拖动              |
| `style`      | `ComputedRef<string>`  | CSS 样式字符串 `left: ?px; top: ?px;` |

### 选项

```ts
useDraggable(el, {
  // 初始位置（默认：{ x: 0, y: 0 }）
  initialValue: { x: 40, y: 40 },
  // 将拖动限制为特定轴：'x'、'y' 或 'both'（默认）
  axis: 'both',
  // 仅在直接点击元素时触发（默认：false）
  exact: false,
  // 防止默认浏览器行为（默认：false）
  preventDefault: true,
  // 停止事件传播（默认：false）
  stopPropagation: false,
  // 对事件使用捕获阶段（默认：true）
  capture: true,
  // 禁用拖动（默认：false）
  disabled: false,
  // 触发拖动的鼠标按钮（默认：[0] - 左键）
  buttons: [0],
  // 要监听的指针类型（默认：['mouse', 'touch', 'pen']）
  pointerTypes: ['mouse', 'touch', 'pen'],
  // 自定义拖动句柄元素（默认：目标元素）
  handle: handleRef,
  // 边界的容器元素（默认：无）
  containerElement: containerRef,
  // 要附加 pointermove/pointerup 事件的元素（默认：window）
  draggingElement: window,
  // 回调
  onStart: (position, event) => {
    // 返回 false 以防止拖动
  },
  onMove: (position, event) => {},
  onEnd: (position, event) => {},
})
```

### 防止默认

设置 `preventDefault: true` 以覆盖浏览器中某些元素的默认拖放行为（例如，图像）。

```ts
import { useDraggable } from '@vueuse/core'

const { x, y, style } = useDraggable(el, {
  preventDefault: true,
})
```

### 容器边界

使用 `containerElement` 将拖动限制在容器内。

```ts
import { useDraggable } from '@vueuse/core'

const { x, y } = useDraggable(el, {
  containerElement: containerRef,
})
```

设置 `autoScroll: true` 以在拖动到边缘附近时启用自动滚动。

```ts
const { x, y, style } = useDraggable(el, {
  autoScroll: {
    speed:2, // 控制自动滚动的速度。
    margin: 30, // 设置从边缘触发自动滚动的边距。
    direction: 'both' // 确定自动滚动的方向。
  },
})
```

## 组件用法

```vue
<template>
  <UseDraggable v-slot="{ x, y }" :initial-value="{ x: 10, y: 10 }">
    拖动我！我在 {{ x }}, {{ y }}
  </UseDraggable>
</template>
```

对于组件用法，可以将其他属性 `storageKey` 和 `storageType` 传递给组件并启用元素位置的持久化。

```vue
<template>
  <UseDraggable storage-key="vueuse-draggable" storage-type="session">
    刷新页面，我仍然在相同位置！
  </UseDraggable>
</template>
```

## 类型声明

```ts
export interface UseDraggableOptions {
  /**
   * 仅在直接点击元素时开始拖动
   *
   * @default false
   */
  exact?: MaybeRefOrGetter<boolean>
  /**
   * 防止事件默认值
   *
   * @default false
   */
  preventDefault?: MaybeRefOrGetter<boolean>
  /**
   * 防止事件传播
   *
   * @default false
   */
  stopPropagation?: MaybeRefOrGetter<boolean>
  /**
   * 是否在捕获阶段调度事件
   *
   * @default true
   */
  capture?: boolean
  /**
   * 要附加 `pointermove` 和 `pointerup` 事件的元素。
   *
   * @default window
   */
  draggingElement?: MaybeRefOrGetter<
    HTMLElement | SVGElement | Window | Document | null | undefined
  >
  /**
   * 用于计算边界的元素（如果未设置，它将使用事件的目标）。
   *
   * @default undefined
   */
  containerElement?: MaybeRefOrGetter<
    HTMLElement | SVGElement | null | undefined
  >
  /**
   * 触发拖动事件的句柄
   *
   * @default target
   */
  handle?: MaybeRefOrGetter<HTMLElement | SVGElement | null | undefined>
  /**
   * 要监听的指针类型。
   *
   * @default ['mouse', 'touch', 'pen']
   */
  pointerTypes?: PointerType[]
  /**
   * 元素的初始位置。
   *
   * @default { x: 0, y: 0 }
   */
  initialValue?: MaybeRefOrGetter<Position>
  /**
   * 开始拖动时的回调。返回 `false` 以防止拖动。
   */
  onStart?: (position: Position, event: PointerEvent) => void | false
  /**
   * 拖动期间的回调。
   */
  onMove?: (position: Position, event: PointerEvent) => void
  /**
   * 拖动结束时的回调。
   */
  onEnd?: (position: Position, event: PointerEvent) => void
  /**
   * 要拖动的轴。
   *
   * @default 'both'
   */
  axis?: "x" | "y" | "both"
  /**
   * 禁用拖放。
   *
   * @default false
   */
  disabled?: MaybeRefOrGetter<boolean>
  /**
   * 允许触发拖动事件的鼠标按钮。
   *
   * - `0`: 主按钮，通常为左键或未初始化状态
   * - `1`: 辅助按钮，通常为滚轮按钮或中键（如果存在）
   * - `2`: 次要按钮，通常为右键
   * - `3`: 第四按钮，通常为浏览器后退按钮
   * - `4`: 第五按钮，通常为浏览器前进按钮
   *
   * @see https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/button#value
   * @default [0]
   */
  buttons?: MaybeRefOrGetter<number[]>
  /**
   * 是否将拖动限制在容器的可见区域内。
   *
   * 如果启用，可拖动元素将不会离开其容器的可见区域，
   * 确保它在拖动期间保持在容器的视口内。
   *
   * @default false
   */
  restrictInView?: MaybeRefOrGetter<boolean>
  /**
   * 是否在拖动到边缘附近时启用自动滚动。
   *
   * @default false
   */
  autoScroll?: MaybeRefOrGetter<
    | boolean
    | {
        /**
         * 自动滚动的速度。
         *
         * @default 2
         */
        speed?: MaybeRefOrGetter<number | Position>
        /**
         * 从边缘触发自动滚动的边距。
         *
         * @default 30
         */
        margin?: MaybeRefOrGetter<number | Position>
        /**
         * 自动滚动的方向。
         *
         * @default 'both'
         */
        direction?: "x" | "y" | "both"
      }
  >
}
export interface UseDraggableReturn {
  x: Ref<number>
  y: Ref<number>
  position: Ref<Position>
  isDragging: ComputedRef<boolean>
  style: ComputedRef<string>
}
/**
 * 使元素可拖动。
 *
 * @see https://vueuse.org/useDraggable
 * @param target
 * @param options
 */
export declare function useDraggable(
  target: MaybeRefOrGetter<HTMLElement | SVGElement | null | undefined>,
  options?: UseDraggableOptions,
): UseDraggableReturn
```
