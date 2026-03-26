---
category: Component
---

# tryOnBeforeUnmount

安全的 `onBeforeUnmount`。如果它在组件生命周期内,则调用 `onBeforeUnmount()`,否则,什么都不做

## 用法

```ts
import { tryOnBeforeUnmount } from '@vueuse/core'

tryOnBeforeUnmount(() => {

})
```

## 类型声明

```ts
/**
 * 如果它在组件生命周期内,则调用 onBeforeUnmount(),否则,什么都不做
 *
 * @param fn
 * @param target
 */
export declare function tryOnBeforeUnmount(
  fn: Fn,
  target?: ComponentInternalInstance | null,
): void
```
