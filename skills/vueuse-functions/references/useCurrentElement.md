---
category: Component
---

# useCurrentElement

获取当前组件的 DOM 元素作为 ref。

## 用法

```ts
import { useCurrentElement } from '@vueuse/core'

const el = useCurrentElement() // ComputedRef<Element>
```

或传递特定的 vue 组件

```vue
<script setup lang="ts">
import { useCurrentElement, VueInstance } from '@vueuse/core'
import { shallowRef } from 'vue'

const componentRef = shallowRef<VueInstance>(null as unknown as VueInstance)

const el = useCurrentElement(componentRef) // ComputedRef<Element>
</script>

<template>
  <div>
    <OtherVueComponent ref="componentRef" />
    <p>Hello world</p>
  </div>
</template>
```

## 注意事项

此函数在底层使用 [`$el`](https://vuejs.org/api/component-instance.html#el)。

在组件挂载之前，ref 的值将是 `undefined`。

- 对于具有单个根元素的组件，它将指向该元素。
- 对于具有文本根的组件，它将指向文本节点。
- 对于具有多个根节点的组件，它将是 Vue 用来跟踪组件在 DOM 中位置的占位符 DOM 节点。

建议仅对具有**单个根元素**的组件使用此函数。

## 类型声明

```ts
export declare function useCurrentElement<
  T extends MaybeElement = MaybeElement,
  R extends VueInstance = VueInstance,
  E extends MaybeElement = MaybeElement extends T
    ? IsAny<R["$el"]> extends false
      ? R["$el"]
      : T
    : T,
>(rootComponent?: MaybeElementRef<R>): ComputedRefWithControl<E>
```
