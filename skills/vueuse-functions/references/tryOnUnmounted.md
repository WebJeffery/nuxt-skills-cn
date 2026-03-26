---
category: Component
---

# tryOnUnmounted

安全的 `onUnmounted`。如果它在组件生命周期内,则调用 `onUnmounted()`,否则,什么都不做

## 用法

```ts
import { tryOnUnmounted } from '@vueuse/core'

tryOnUnmounted(() => {

})
```

## 类型声明

```ts
/**
 * 如果它在组件生命周期内,则调用 onUnmounted(),否则,什么都不做
 *
 * @param fn
 * @param target
 */
export declare function tryOnUnmounted(
  fn: Fn,
  target?: ComponentInternalInstance | null,
): void
```
