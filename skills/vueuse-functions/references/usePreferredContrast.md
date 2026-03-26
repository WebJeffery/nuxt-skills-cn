---
category: Browser
---

# usePreferredContrast

响应式 [prefers-contrast](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-contrast) 媒体查询。

## 用法

```ts
import { usePreferredContrast } from '@vueuse/core'

const preferredContrast = usePreferredContrast()
```

## 组件用法

```vue
<template>
  <UsePreferredContrast v-slot="{ contrast }">
    Preferred Contrast: {{ contrast }}
  </UsePreferredContrast>
</template>
```

## 类型声明

```ts
export type ContrastType = "more" | "less" | "custom" | "no-preference"
/**
 * 响应式 prefers-contrast 媒体查询。
 *
 * @see https://vueuse.org/usePreferredContrast
 * @param [options]
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePreferredContrast(
  options?: ConfigurableWindow,
): ComputedRef<ContrastType>
```
