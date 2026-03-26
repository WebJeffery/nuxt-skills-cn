---
category: Reactivity
alias: eagerComputed
---

# computedEager

没有懒加载评估的急切计算。

::: info
此函数将在未来版本中被移除。
:::

::: tip
注意💡: 如果您使用的是 Vue 3.4+,您可以直接使用 `computed`,不再需要此函数。
在 Vue 3.4+ 中,如果计算的新值没有改变,`computed`、`effect`、`watch`、`watchEffect`、`render` 依赖项将不会被触发。
参见: https://github.com/vuejs/core/pull/5912
:::

在 [Vue: When a computed property can be the wrong tool](https://dev.to/linusborg/vue-when-a-computed-property-can-be-the-wrong-tool-195j) 了解更多信息。

- 当您进行复杂的计算时,使用 `computed()`,这实际上可以从缓存和懒加载中受益,并且只有在真正必要时才应该(重新)计算。
- 当您进行简单的操作,且返回值很少变化时,使用 `computedEager()` - 通常是一个布尔值。

## 用法

```ts
import { computedEager } from '@vueuse/core'

const todos = ref([])
const hasOpenTodos = computedEager(() => !!todos.length)

console.log(hasOpenTodos.value) // false
toDos.value.push({ title: 'Learn Vue' })
console.log(hasOpenTodos.value) // true
```

## 类型声明

```ts
export type ComputedEagerOptions = WatchOptionsBase
export type ComputedEagerReturn<T = any> = Readonly<ShallowRef<T>>
/**
 *
 * @deprecated 此函数将在未来版本中被移除。
 *
 * 注意: 如果您使用的是 Vue 3.4+,您可以直接使用 computed 代替。
 * 因为在 Vue 3.4+ 中,如果计算的新值没有改变,
 * computed、effect、watch、watchEffect、render 依赖项将不会被触发。
 * 参考: https://github.com/vuejs/core/pull/5912
 *
 * @param fn effect 函数
 * @param options WatchOptionsBase
 * @returns readonly shallowRef
 */
export declare function computedEager<T>(
  fn: () => T,
  options?: ComputedEagerOptions,
): ComputedEagerReturn<T>
/** @deprecated 使用 `computedEager` 代替 */
export declare const eagerComputed: typeof computedEager
```
