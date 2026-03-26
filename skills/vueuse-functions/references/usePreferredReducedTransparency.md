---
category: Browser
---

# usePreferredReducedTransparency

响应式 [prefers-reduced-transparency](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-transparency) 媒体查询。

## 用法

```ts
import { usePreferredReducedTransparency } from '@vueuse/core'

const preferredTransparency = usePreferredReducedTransparency()
```

## 组件用法

```vue
<template>
  <UsePreferredReducedTransparency v-slot="{ transparency }">
    Preferred Reduced transparency: {{ transparency }}
  </UsePreferredReducedTransparency>
</template>
```

## 类型声明

```ts
export type ReducedTransparencyType = "reduce" | "no-preference"
/**
 * 响应式 prefers-reduced-transparency 媒体查询。
 *
 * @see https://vueuse.org/usePreferredReducedTransparency
 * @param [options]
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePreferredReducedTransparency(
  options?: ConfigurableWindow,
): ComputedRef<ReducedTransparencyType>
```
