---
category: Elements
---

# useIntersectionObserver

检测目标元素的可见性。

## 用法

```vue
<script setup lang="ts">
import { useIntersectionObserver } from '@vueuse/core'
import { shallowRef, useTemplateRef } from 'vue'

const target = useTemplateRef('target')
const targetIsVisible = shallowRef(false)

const { stop } = useIntersectionObserver(
  target,
  ([entry], observerElement) => {
    targetIsVisible.value = entry?.isIntersecting || false
  },
)
</script>

<template>
  <div ref="target">
    <h1>你好世界</h1>
  </div>
</template>
```

## 指令用法

```vue
<script setup lang="ts">
import { vIntersectionObserver } from '@vueuse/components'
import { shallowRef, useTemplateRef } from 'vue'

const root = useTemplateRef('root')

const isVisible = shallowRef(false)

function onIntersectionObserver([entry]: IntersectionObserverEntry[]) {
  isVisible.value = entry?.isIntersecting || false
}
</script>

<template>
  <div>
    <p>
      向下滚动！
    </p>
    <div v-intersection-observer="onIntersectionObserver">
      <p>你好世界！</p>
    </div>
  </div>

  <!-- 带选项 -->
  <div ref="root">
    <p>
      向下滚动！
    </p>
    <div v-intersection-observer="[onIntersectionObserver, { root }]">
      <p>你好世界！</p>
    </div>
  </div>
</template>
```

[IntersectionObserver MDN](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver/IntersectionObserver)

## Type Declarations

```ts
export interface UseIntersectionObserverOptions extends ConfigurableWindow {
  /**
   * Start the IntersectionObserver immediately on creation
   *
   * @default true
   */
  immediate?: boolean
  /**
   * The Element or Document whose bounds are used as the bounding box when testing for intersection.
   */
  root?: MaybeComputedElementRef | Document
  /**
   * A string which specifies a set of offsets to add to the root's bounding_box when calculating intersections.
   */
  rootMargin?: MaybeRefOrGetter<string>
  /**
   * Either a single number or an array of numbers between 0.0 and 1.
   * @default 0
   */
  threshold?: number | number[]
}
export interface UseIntersectionObserverReturn extends Supportable, Pausable {
  stop: () => void
}
/**
 * Detects that a target element's visibility.
 *
 * @see https://vueuse.org/useIntersectionObserver
 * @param target
 * @param callback
 * @param options
 */
export declare function useIntersectionObserver(
  target:
    | MaybeComputedElementRef
    | MaybeRefOrGetter<MaybeElement[]>
    | MaybeComputedElementRef[],
  callback: IntersectionObserverCallback,
  options?: UseIntersectionObserverOptions,
): UseIntersectionObserverReturn
```
