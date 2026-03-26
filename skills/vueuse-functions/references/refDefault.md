---
category: Reactivity
---

# refDefault

将默认值应用于 ref。

## 用法

```ts
import { refDefault, useStorage } from '@vueuse/core'

const raw = useStorage('key')
const state = refDefault(raw, 'default')

raw.value = 'hello'
console.log(state.value) // hello

raw.value = undefined
console.log(state.value) // default
```

## 类型声明

```ts
/**
 * 将默认值应用于 ref。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function refDefault<T>(
  source: Ref<T | undefined | null>,
  defaultValue: T,
): Ref<T>
```
