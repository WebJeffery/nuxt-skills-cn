---
category: Browser
---

# useScreenSafeArea

响应式 `env(safe-area-inset-*)`

![image](https://webkit.org/wp-content/uploads/safe-areas-1.png)

## 用法

为了使页面在屏幕上完全渲染，必须在 `viewport` meta 标签中首先设置附加属性 `viewport-fit=cover`，viewport meta 标签可能如下所示：

```html
<meta name="viewport" content="initial-scale=1, viewport-fit=cover" />
```

然后我们可以在组件中使用 `useScreenSafeArea`，如下所示：

```ts
import { useScreenSafeArea } from '@vueuse/core'

const {
  top,
  right,
  bottom,
  left,
} = useScreenSafeArea()
```

有关更多详细信息，您可以参考此文档：[为 iPhone X 设计网站](https://webkit.org/blog/7929/designing-websites-for-iphone-x/)

## 组件用法

```vue
<template>
  <UseScreenSafeArea top right bottom left>
    内容
  </UseScreenSafeArea>
</template>
```

## Type Declarations

```ts
export interface UseScreenSafeAreaReturn {
  top: ShallowRef<string>
  right: ShallowRef<string>
  bottom: ShallowRef<string>
  left: ShallowRef<string>
  update: () => void
}
/**
 * Reactive `env(safe-area-inset-*)`
 *
 * @see https://vueuse.org/useScreenSafeArea
 */
export declare function useScreenSafeArea(): UseScreenSafeAreaReturn
```
