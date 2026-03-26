---
category: Elements
---

# useElementVisibility

跟踪元素在视口内的可见性。

## 用法

```vue
<script setup lang="ts">
import { useElementVisibility } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const target = useTemplateRef('target')
const targetIsVisible = useElementVisibility(target)
</script>

<template>
  <div ref="target">
    <h1>你好世界</h1>
  </div>
</template>
```

### rootMargin

如果您希望在元素完全可见之前更早触发回调，可以使用 `rootMargin` 选项（参见 [MDN IntersectionObserver/rootMargin](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver/rootMargin)）。

```ts
import { useElementVisibility } from '@vueuse/core'
// ---cut---
const targetIsVisible = useElementVisibility(target, {
  rootMargin: '0px 0px 100px 0px',
})
```

### threshold

如果您想控制更新值所需的可见性百分比，可以使用 `threshold` 选项（参见 [MDN IntersectionObserver/threshold](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver/IntersectionObserver#threshold)）。

```ts
const targetIsVisible = useElementVisibility(target, {
  threshold: 1.0, // 100% 可见
})
```

## 组件用法

```vue
<template>
  <UseElementVisibility v-slot="{ isVisible }">
    可见: {{ isVisible }}
  </UseElementVisibility>
</template>
```

## 指令用法

```vue
<script setup lang="ts">
import { vElementVisibility } from '@vueuse/components'
import { shallowRef, useTemplateRef } from 'vue'

const target = useTemplateRef('target')
const isVisible = shallowRef(false)

function onElementVisibility(state) {
  isVisible.value = state
}
</script>

<template>
  <div v-element-visibility="onElementVisibility">
    {{ isVisible ? '内部' : '外部' }}
  </div>

  <!-- 带选项 -->
  <div ref="target">
    <div v-element-visibility="[onElementVisibility, { scrollTarget: target }]">
      {{ isVisible ? '内部' : '外部' }}
    </div>
  </div>
</template>
```

## 类型声明

```ts
export interface UseElementVisibilityOptions
  extends
    ConfigurableWindow,
    Pick<UseIntersectionObserverOptions, "rootMargin" | "threshold"> {
  /**
   * 初始值。
   *
   * @default false
   */
  initialValue?: boolean
  /**
   * 用作检查目标可见性的视口的元素。
   */
  scrollTarget?: UseIntersectionObserverOptions["root"]
  /**
   * 元素可见性第一次变化时停止跟踪
   *
   * @default false
   */
  once?: boolean
}
export type UseElementVisibilityReturn = ShallowRef<boolean>
/**
 * 跟踪元素在视口内的可见性。
 *
 * @see https://vueuse.org/useElementVisibility
 */
export declare function useElementVisibility(
  element: MaybeComputedElementRef,
  options?: UseElementVisibilityOptions,
): UseElementVisibilityReturn
```

<!--
Source references:
- https://vueuse.org/core/useElementVisibility/
-->