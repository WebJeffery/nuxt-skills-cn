---
category: Elements
---

# useParentElement

获取给定元素的父元素

## 用法

当不传递参数时，它将返回当前组件的父元素。

```vue
<script setup lang="ts">
import { useParentElement } from '@vueuse/core'

const parentEl = useParentElement()

onMounted(() => {
  console.log(parentEl.value)
})
</script>
```

它也可以接受一个 `ref` 作为第一个参数。

```vue
<script setup lang="ts">
import { useParentElement } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const tooltip = useTemplateRef('tooltip')

const tooltipWrapper = useParentElement(tooltip)

onMounted(() => {
  console.log(tooltipWrapper.value)
})
</script>

<template>
  <div>
    <p ref="tooltip" />
  </div>
</template>
```

## 类型声明

```ts
export declare function useParentElement(
  element?: MaybeRefOrGetter<HTMLElement | SVGElement | null | undefined>,
): Readonly<ShallowRef<HTMLElement | SVGElement | null | undefined>>
```
