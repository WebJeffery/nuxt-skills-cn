---
category: Browser
---

# usePreferredColorScheme

响应式 [prefers-color-scheme](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme) 媒体查询。

## 用法

```ts
import { usePreferredColorScheme } from '@vueuse/core'

const preferredColor = usePreferredColorScheme()
```

## 组件用法

```vue
<template>
  <UsePreferredColorScheme v-slot="{ colorScheme }">
    Preferred Color Scheme: {{ colorScheme }}
  </UsePreferredColorScheme>
</template>
```

## 类型声明

```ts
export type ColorSchemeType = "dark" | "light" | "no-preference"
/**
 * 响应式 prefers-color-scheme 媒体查询。
 *
 * @see https://vueuse.org/usePreferredColorScheme
 * @param [options]
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePreferredColorScheme(
  options?: ConfigurableWindow,
): ComputedRef<ColorSchemeType>
```
