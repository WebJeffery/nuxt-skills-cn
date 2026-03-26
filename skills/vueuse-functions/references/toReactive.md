---
category: Reactivity
---

# toReactive

将 ref 转换为 reactive。还可以创建一个"可交换"的响应式对象。

## 用法

```ts
import { toReactive } from '@vueuse/core'
import { ref } from 'vue'

const refState = ref({ foo: 'bar' })

console.log(refState.value.foo) // => 'bar'

const state = toReactive(refState) // <--

console.log(state.foo) // => 'bar'

refState.value = { bar: 'foo' }

console.log(state.foo) // => undefined
console.log(state.bar) // => 'foo'
```

## 类型声明

```ts
/**
 * 将 ref 转换为 reactive。
 *
 * @see https://vueuse.org/toReactive
 * @param objectRef 对象的 ref
 */
export declare function toReactive<T extends object>(
  objectRef: MaybeRef<T>,
): UnwrapNestedRefs<T>
```
