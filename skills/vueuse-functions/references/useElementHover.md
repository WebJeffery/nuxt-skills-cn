---
category: Sensors
---

# useElementHover

响应式元素的悬停状态。

## 用法

```vue
<script setup lang="ts">
import { useElementHover } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const myHoverableElement = useTemplateRef('myHoverableElement')
const isHovered = useElementHover(myHoverableElement)
</script>

<template>
  <button ref="myHoverableElement">
    {{ isHovered }}
  </button>
</template>
```

## 指令用法

```vue
<script setup lang="ts">
import { vElementHover } from '@vueuse/components'
import { shallowRef } from 'vue'

const isHovered = shallowRef(false)
function onHover(state: boolean) {
  isHovered.value = state
}
</script>

<template>
  <button v-element-hover="onHover">
    {{ isHovered ? '谢谢!' : '悬停我' }}
  </button>
</template>
```

您还可以提供悬停选项：

```vue
<script setup lang="ts">
import { vElementHover } from '@vueuse/components'
import { shallowRef } from 'vue'

const isHovered = shallowRef(false)
function onHover(hovered: boolean) {
  isHovered.value = hovered
}
</script>

<template>
  <button v-element-hover="[onHover, { delayEnter: 1000 }]">
    <span>{{ isHovered ? '谢谢!' : '悬停我' }}</span>
  </button>
</template>
```

## 类型声明

```ts
export interface UseElementHoverOptions extends ConfigurableWindow {
  delayEnter?: number
  delayLeave?: number
  triggerOnRemoval?: boolean
}
export declare function useElementHover(
  el: MaybeRefOrGetter<EventTarget | null | undefined>,
  options?: UseElementHoverOptions,
): ShallowRef<boolean>
```

<!--
Source references:
- https://vueuse.org/core/useElementHover/
-->