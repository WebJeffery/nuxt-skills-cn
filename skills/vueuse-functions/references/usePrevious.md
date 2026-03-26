---
category: Utilities
---

# usePrevious

保持 ref 的前一个值。

## 用法

```ts
import { usePrevious } from '@vueuse/core'
import { shallowRef } from 'vue'

const counter = shallowRef('Hello')
const previous = usePrevious(counter)

console.log(previous.value) // undefined

counter.value = 'World'

console.log(previous.value) // Hello
```

## 类型声明

```ts
/**
 * 保持 ref 的前一个值。
 *
 * @see   {@link https://vueuse.org/usePrevious}
 */
export declare function usePrevious<T>(
  value: MaybeRefOrGetter<T>,
): Readonly<ShallowRef<T | undefined>>
export declare function usePrevious<T>(
  value: MaybeRefOrGetter<T>,
  initialValue: T,
): Readonly<ShallowRef<T>>
```
