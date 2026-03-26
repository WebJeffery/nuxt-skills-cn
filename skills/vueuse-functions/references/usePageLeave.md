---
category: Sensors
---

# usePageLeave

响应式状态，显示鼠标是否离开页面。

## 用法

```ts
import { usePageLeave } from '@vueuse/core'

const isLeft = usePageLeave()
```

## 组件用法

```vue
<template>
  <UsePageLeave v-slot="{ isLeft }">
    Has Left Page: {{ isLeft }}
  </UsePageLeave>
</template>
```

## 类型声明

```ts
export interface UsePageLeaveOptions extends ConfigurableWindow {}
export type UsePageLeaveReturn = ShallowRef<boolean>
/**
 * 响应式状态，显示鼠标是否离开页面。
 *
 * @see https://vueuse.org/usePageLeave
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function usePageLeave(
  options?: UsePageLeaveOptions,
): UsePageLeaveReturn
```
