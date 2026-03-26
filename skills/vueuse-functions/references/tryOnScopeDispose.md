---
category: Component
---

# tryOnScopeDispose

安全的 `onScopeDispose`。如果它在 effect scope 生命周期内,则调用 `onScopeDispose()`,否则,什么都不做

## 用法

```ts
import { tryOnScopeDispose } from '@vueuse/core'

tryOnScopeDispose(() => {

})
```

## 类型声明

```ts
/**
 * 如果它在 effect scope 生命周期内,则调用 onScopeDispose(),否则,什么都不做
 *
 * @param fn
 */
export declare function tryOnScopeDispose(
  fn: Fn,
  failSilently?: boolean,
): boolean
```
