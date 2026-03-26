---
category: Elements
---

# useActiveElement

响应式 `document.activeElement`。返回一个浅层 ref,当焦点更改时更新。

## 用法

```vue
<script setup lang="ts">
import { useActiveElement } from '@vueuse/core'
import { watch } from 'vue'

const activeElement = useActiveElement()

watch(activeElement, (el) => {
  console.log('focus changed to', el)
})
</script>
```

### Shadow DOM 支持

默认情况下,`useActiveElement` 将遍历到 shadow DOM 以查找深度活动的元素。设置 `deep: false` 以禁用此行为。

```ts
import { useActiveElement } from '@vueuse/core'

// 只获取 shadow host,而不是 shadow DOM 内部的元素
const activeElement = useActiveElement({ deep: false })
```

### 跟踪元素移除

设置 `triggerOnRemoval: true` 以在当前活动元素从 DOM 中移除时更新活动元素。这在底层使用 `MutationObserver`。

```ts
import { useActiveElement } from '@vueuse/core'

const activeElement = useActiveElement({ triggerOnRemoval: true })
```

## 组件用法

```vue
<template>
  <UseActiveElement v-slot="{ element }">
    Active element is {{ element?.dataset.id }}
  </UseActiveElement>
</template>
```

## 类型声明

```ts
export interface UseActiveElementOptions
  extends ConfigurableWindow, ConfigurableDocumentOrShadowRoot {
  /**
   * 在 shadow dom 内深度搜索活动元素
   *
   * @default true
   */
  deep?: boolean
  /**
   * 当活动元素从 DOM 中移除时跟踪活动元素
   * 在底层使用 MutationObserver
   * @default false
   */
  triggerOnRemoval?: boolean
}
export type UseActiveElementReturn<T extends HTMLElement = HTMLElement> =
  ShallowRef<T | null | undefined>
/**
 * 响应式 `document.activeElement`
 *
 * @see https://vueuse.org/useActiveElement
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useActiveElement<T extends HTMLElement>(
  options?: UseActiveElementOptions,
): UseActiveElementReturn<T>
```
