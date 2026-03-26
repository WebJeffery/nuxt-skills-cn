---
category: State
---

# provideLocal

扩展的 `provide`,具有在同一组件中调用 `injectLocal` 以获取值的能力。

## 用法

```vue
<script setup>
import { injectLocal, provideLocal } from '@vueuse/core'

provideLocal('MyInjectionKey', 1)
const injectedValue = injectLocal('MyInjectionKey') // injectedValue === 1
</script>
```

## 类型声明

```ts
export type ProvideLocalReturn = void
/**
 * 在 `provide` 的基础上,允许在同一组件中调用 provide 后直接调用 inject 获取值。
 *
 * @example
 * ```ts
 * provideLocal('MyInjectionKey', 1)
 * const injectedValue = injectLocal('MyInjectionKey') // injectedValue === 1
 * ```
 */
export declare function provideLocal<T, K = LocalProvidedKey<T>>(
  key: K,
  value: K extends InjectionKey<infer V> ? V : T,
): ProvideLocalReturn
```
