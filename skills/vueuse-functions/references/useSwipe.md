---
category: Sensors
---

# useSwipe

基于 [`TouchEvents`](https://developer.mozilla.org/en-US/docs/Web/API/TouchEvent) 响应式滑动检测。

## 用法

```vue
<script setup lang="ts">
import { useSwipe } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const { isSwiping, direction } = useSwipe(el)
</script>

<template>
  <div ref="el">
    Swipe here
  </div>
</template>
```

## 类型声明

```ts
export type UseSwipeDirection = "up" | "down" | "left" | "right" | "none"
export interface UseSwipeOptions extends ConfigurableWindow {
  /**
   * 将事件注册为被动
   *
   * @default true
   */
  passive?: boolean
  /**
   * @default 50
   */
  threshold?: number
  /**
   * 滑动开始时的回调
   */
  onSwipeStart?: (e: TouchEvent) => void
  /**
   * 滑动移动时的回调
   */
  onSwipe?: (e: TouchEvent) => void
  /**
   * 滑动结束时的回调
   */
  onSwipeEnd?: (e: TouchEvent, direction: UseSwipeDirection) => void
}
export interface UseSwipeReturn {
  isSwiping: ShallowRef<boolean>
  direction: ComputedRef<UseSwipeDirection>
  coordsStart: Readonly<Position>
  coordsEnd: Readonly<Position>
  lengthX: ComputedRef<number>
  lengthY: ComputedRef<number>
  stop: () => void
}
/**
 * 响应式滑动检测。
 *
 * @see https://vueuse.org/useSwipe
 * @param target
 * @param options
 */
export declare function useSwipe(
  target: MaybeRefOrGetter<EventTarget | null | undefined>,
  options?: UseSwipeOptions,
): UseSwipeReturn
```
