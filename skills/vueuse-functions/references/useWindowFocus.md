---
category: Elements
---

# useWindowFocus

使用 `window.onfocus` 和 `window.onblur` 事件响应式跟踪窗口焦点。

## 用法

```vue
<script setup lang="ts">
import { useWindowFocus } from '@vueuse/core'

const focused = useWindowFocus()
</script>

<template>
  <div>{{ focused }}</div>
</template>
```

## 组件用法

```vue
<template>
  <UseWindowFocus v-slot="{ focused }">
    文档焦点: {{ focused }}
  </UseWindowFocus>
</template>
```

## 类型声明

```ts
/**
 * 使用 `window.onfocus` 和 `window.onblur` 响应式跟踪窗口焦点。
 *
 * @see https://vueuse.org/useWindowFocus
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useWindowFocus(
  options?: ConfigurableWindow,
): ShallowRef<boolean>
```
