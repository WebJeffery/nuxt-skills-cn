---
category: Browser
---

# usePreferredLanguages

响应式 [Navigator Languages](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorLanguage/languages)。它为 Web 开发人员提供有关用户首选语言的信息。例如，这对于根据用户的首选语言调整用户界面的语言以提供更好的体验可能很有用。

## 用法

```ts
import { usePreferredLanguages } from '@vueuse/core'

const languages = usePreferredLanguages()
```

## 组件用法

```vue
<template>
  <UsePreferredLanguages v-slot="{ languages }">
    Preferred Languages: {{ languages }}
  </UsePreferredLanguages>
</template>
```

## 类型声明

```ts
/**
 * 响应式 Navigator Languages。
 *
 * @see https://vueuse.org/usePreferredLanguages
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePreferredLanguages(
  options?: ConfigurableWindow,
): ShallowRef<readonly string[]>
```
