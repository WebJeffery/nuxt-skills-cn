---
category: Browser
---

# usePreferredReducedMotion

响应式 [prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) 媒体查询。

## 用法

```ts
import { usePreferredReducedMotion } from '@vueuse/core'

const preferredMotion = usePreferredReducedMotion()
```

## 组件用法

```vue
<template>
  <UsePreferredReducedMotion v-slot="{ motion }">
    Preferred Reduced Motion: {{ motion }}
  </UsePreferredReducedMotion>
</template>
```

## 类型声明

```ts
export type ReducedMotionType = "reduce" | "no-preference"
/**
 * 响应式 prefers-reduced-motion 媒体查询。
 *
 * @see https://vueuse.org/usePreferredReducedMotion
 * @param [options]
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePreferredReducedMotion(
  options?: ConfigurableWindow,
): ComputedRef<ReducedMotionType>
```
