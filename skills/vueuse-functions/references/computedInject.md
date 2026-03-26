---
category: Component
---

# computedInject

结合 `computed` 和 `inject`。用于基于注入的值创建计算属性。

## 用法

在提供者组件中

```ts twoslash include main
import type { InjectionKey, Ref } from 'vue'
import { provide, ref } from 'vue'

interface Item {
  key: number
  value: string
}

export const ArrayKey: InjectionKey<Ref<Item[]>> = Symbol('symbol-key')

const array = ref([{ key: 1, value: '1' }, { key: 2, value: '2' }, { key: 3, value: '3' }])

provide(ArrayKey, array)
```

在接收者组件中

```ts
// @filename: provider.ts
// @include: main
// ---cut---
import { computedInject } from '@vueuse/core'

import { ArrayKey } from './provider'

const computedArray = computedInject(ArrayKey, (source) => {
  const arr = [...source.value]
  arr.unshift({ key: 0, value: 'all' })
  return arr
})
```

### 默认值

您可以提供一个默认值,如果父组件没有提供注入键,则使用该默认值。

```ts
import { computedInject } from '@vueuse/core'

const computedArray = computedInject(
  ArrayKey,
  (source) => {
    return source.value.map(item => item.value)
  },
  ref([]), // 默认源值
)
```

### 工厂默认值

将 `true` 作为第四个参数传递,将默认值视为工厂函数。

```ts
import { computedInject } from '@vueuse/core'

const computedArray = computedInject(
  ArrayKey,
  (source) => {
    return source.value.map(item => item.value)
  },
  () => ref([]), // 默认值的工厂函数
  true, // 将默认值视为工厂
)
```

### 可写计算

您还可以通过传递带有 `get` 和 `set` 函数的对象来创建可写计算属性。

```ts
import { computedInject } from '@vueuse/core'

const computedArray = computedInject(ArrayKey, {
  get(source) {
    return source.value.map(item => item.value)
  },
  set(value) {
    // 处理设置值
    console.log('Setting value:', value)
  },
})
```

## 类型声明

```ts
export type ComputedInjectGetter<T, K> = (
  source: T | undefined,
  oldValue?: K,
) => K
export type ComputedInjectGetterWithDefault<T, K> = (
  source: T,
  oldValue?: K,
) => K
export type ComputedInjectSetter<T> = (v: T) => void
export interface WritableComputedInjectOptions<T, K> {
  get: ComputedInjectGetter<T, K>
  set: ComputedInjectSetter<K>
}
export interface WritableComputedInjectOptionsWithDefault<T, K> {
  get: ComputedInjectGetterWithDefault<T, K>
  set: ComputedInjectSetter<K>
}
export declare function computedInject<T, K = any>(
  key: InjectionKey<T> | string,
  getter: ComputedInjectGetter<T, K>,
): ComputedRef<K | undefined>
export declare function computedInject<T, K = any>(
  key: InjectionKey<T> | string,
  options: WritableComputedInjectOptions<T, K>,
): ComputedRef<K | undefined>
export declare function computedInject<T, K = any>(
  key: InjectionKey<T> | string,
  getter: ComputedInjectGetterWithDefault<T, K>,
  defaultSource: T,
  treatDefaultAsFactory?: false,
): ComputedRef<K>
export declare function computedInject<T, K = any>(
  key: InjectionKey<T> | string,
  options: WritableComputedInjectOptionsWithDefault<T, K>,
  defaultSource: T | (() => T),
  treatDefaultAsFactory: true,
): ComputedRef<K>
```
