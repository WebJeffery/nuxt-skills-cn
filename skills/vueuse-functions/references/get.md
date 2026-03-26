---
category: Utilities
---

# get

访问 `ref.value` 的简写

## 用法

```ts
import { get } from '@vueuse/core'

const a = ref(42)

console.log(get(a)) // 42
```

## 类型声明

```ts
/**
 * 访问 `ref.value` 的简写
 */
export declare function get<T>(ref: MaybeRef<T>): T
export declare function get<T, K extends keyof T>(
  ref: MaybeRef<T>,
  key: K,
): T[K]
```
