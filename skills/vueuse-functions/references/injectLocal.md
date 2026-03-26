---
category: State
---

# injectLocal

扩展的 `inject`,具有在同一组件中调用 `provideLocal` 以提供值的能力。

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
/**
 * 在 `inject` 的基础上,允许在同一组件中调用 provide 后直接调用 inject 获取值。
 *
 * @example
 * ```ts
 * injectLocal('MyInjectionKey', 1)
 * const injectedValue = injectLocal('MyInjectionKey') // injectedValue === 1
 * ```
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare const injectLocal: typeof inject
```
