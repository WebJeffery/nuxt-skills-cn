---
category: Component
---

# tryOnMounted

安全的 `onMounted`。如果它在组件生命周期内,则调用 `onMounted()`,否则,只调用函数

## 用法

```ts
import { tryOnMounted } from '@vueuse/core'

tryOnMounted(() => {

})
```

## 类型声明

```ts
/**
 * 如果它在组件生命周期内,则调用 onMounted(),否则,只调用函数
 *
 * @param fn
 * @param sync 如果设置为 false,它将在 Vue 的 nextTick() 中运行
 * @param target
 */
export declare function tryOnMounted(
  fn: Fn,
  sync?: boolean,
  target?: ComponentInternalInstance | null,
): void
```
