---
category: Utilities
---

# useSupported

SSR 兼容性 `isSupported`

## 用法

```ts
import { useSupported } from '@vueuse/core'

const isSupported = useSupported(() => navigator && 'getBattery' in navigator)

if (isSupported.value) {
  // 做一些事情
  navigator.getBattery
}
```

## 类型声明

```ts
export type UseSupportedReturn = ComputedRef<boolean>
export declare function useSupported(
  callback: () => unknown,
): UseSupportedReturn
```
