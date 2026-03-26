---
category: Utilities
---

# set

`ref.value = x` 的简写

## 用法

```ts
import { set } from '@vueuse/core'

const a = ref(0)

set(a, 1)

console.log(a.value) // 1
```

## 类型声明

```ts
export declare function set<T>(ref: Ref<T>, value: T): void
export declare function set<O extends object, K extends keyof O>(
  target: O,
  key: K,
  value: O[K],
): void
```
