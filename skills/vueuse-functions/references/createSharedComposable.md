---
category: State
related: createGlobalState
---

# createSharedComposable

使可组合函数可用于多个 Vue 实例。

> [!WARNING]
> 当在 **SSR** 环境中使用时,`createSharedComposable` 将**自动回退**到非共享版本。
> 这意味着每次调用都会在 SSR 中创建一个新实例,以避免[跨请求状态污染](https://vuejs.org/guide/scaling-up/ssr.html#cross-request-state-pollution)。

## 用法

```ts
import { createSharedComposable, useMouse } from '@vueuse/core'

const useSharedMouse = createSharedComposable(useMouse)

// CompA.vue
const { x, y } = useSharedMouse()

// CompB.vue - 将重用之前的状态,不会注册新的事件监听器
const { x, y } = useSharedMouse()
```

## 类型声明

```ts
export type SharedComposableReturn<T extends AnyFn = AnyFn> = T
/**
 * 使可组合函数可用于多个 Vue 实例。
 *
 * @see https://vueuse.org/createSharedComposable
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function createSharedComposable<Fn extends AnyFn>(
  composable: Fn,
): SharedComposableReturn<Fn>
```
