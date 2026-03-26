---
category: Reactivity
---

# createRef

根据 `deep` 参数返回 `deepRef` 或 `shallowRef`。

## 用法

```ts
import { createRef } from '@vueuse/core'
import { isShallow, ref } from 'vue'

const initialData = 1

const shallowData = createRef(initialData)
const deepData = createRef(initialData, true)

isShallow(shallowData) // true
isShallow(deepData) // false
```

## 类型声明

```ts
export type CreateRefReturn<
  T = any,
  D extends boolean = false,
> = ShallowOrDeepRef<T, D>
export type ShallowOrDeepRef<
  T = any,
  D extends boolean = false,
> = D extends true ? Ref<T> : ShallowRef<T>
/**
 * 根据 `deep` 参数返回 `deepRef` 或 `shallowRef`。
 *
 * @example createRef(1) // ShallowRef<number>
 * @example createRef(1, false) // ShallowRef<number>
 * @example createRef(1, true) // Ref<number>
 * @example createRef("string") // ShallowRef<string>
 * @example createRef<"A"|"B">("A", true) // Ref<"A"|"B">
 *
 * @param value
 * @param deep
 * @returns `deepRef` 或 `shallowRef`
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function createRef<T = any, D extends boolean = false>(
  value: T,
  deep?: D,
): CreateRefReturn<T, D>
```
