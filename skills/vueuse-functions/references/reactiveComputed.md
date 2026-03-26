---
category: Reactivity
---

# reactiveComputed

计算响应式对象。`reactiveComputed` 返回响应式对象,而不是像 `computed` 那样返回 ref。

## 用法

```ts
import { reactiveComputed } from '@vueuse/core'

const state = reactiveComputed(() => {
  return {
    foo: 'bar',
    bar: 'baz',
  }
})

state.bar // 'baz'
```

## 类型声明

```ts
export type ReactiveComputedReturn<T extends object> = UnwrapNestedRefs<T>
/**
 * 计算响应式对象。
 */
export declare function reactiveComputed<T extends object>(
  fn: ComputedGetter<T>,
): ReactiveComputedReturn<T>
```
