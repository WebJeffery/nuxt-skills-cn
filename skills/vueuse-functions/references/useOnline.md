---
category: Sensors
---

# useOnline

响应式在线状态。`useNetwork` 的包装器。

## 用法

```ts
import { useOnline } from '@vueuse/core'

const online = useOnline()
```

## 组件用法

```vue
<template>
  <UseOnline v-slot="{ isOnline }">
    Is Online: {{ isOnline }}
  </UseOnline>
</template>
```

## 类型声明

```ts
/**
 * 响应式在线状态。
 *
 * @see https://vueuse.org/useOnline
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useOnline(
  options?: ConfigurableWindow,
): Readonly<ShallowRef<boolean>>
```
