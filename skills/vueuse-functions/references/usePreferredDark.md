---
category: Browser
---

# usePreferredDark

响应式深色主题偏好。

## 用法

```ts
import { usePreferredDark } from '@vueuse/core'

const isDark = usePreferredDark()
```

## 组件用法

```vue
<template>
  <UsePreferredDark v-slot="{ prefersDark }">
    Prefers Dark: {{ prefersDark }}
  </UsePreferredDark>
</template>
```

## 类型声明

```ts
/**
 * 响应式深色主题偏好。
 *
 * @see https://vueuse.org/usePreferredDark
 * @param [options]
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePreferredDark(
  options?: ConfigurableWindow,
): ComputedRef<boolean>
```
