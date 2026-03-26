---
category: Reactivity
---

# extendRef

向 Ref 添加额外的属性。

## 用法

> 请注意,额外的属性在 Vue 的模板中无法访问。

```ts
import { extendRef } from '@vueuse/core'
import { shallowRef } from 'vue'

const myRef = shallowRef('content')

const extended = extendRef(myRef, { foo: 'extra data' })

extended.value === 'content'
extended.foo === 'extra data'
```

Refs 将被解包并且是响应式的

```ts
import { extendRef } from '@vueuse/core'
// ---cut---
const myRef = shallowRef('content')
const extraRef = shallowRef('extra')

const extended = extendRef(myRef, { extra: extraRef })

extended.value === 'content'
extended.extra === 'extra'

extended.extra = 'new data' // 将触发更新
extraRef.value === 'new data'
```

## 类型声明

```ts
export type ExtendRefReturn<T = any> = Ref<T>
export interface ExtendRefOptions<Unwrap extends boolean = boolean> {
  /**
   * 扩展属性是否可枚举
   *
   * @default false
   */
  enumerable?: boolean
  /**
   * 解包 Ref 属性
   *
   * @default true
   */
  unwrap?: Unwrap
}
/**
 * 重载 1: Unwrap 设置为 false
 */
export declare function extendRef<
  R extends Ref<any>,
  Extend extends object,
  Options extends ExtendRefOptions<false>,
>(ref: R, extend: Extend, options?: Options): ShallowUnwrapRef<Extend> & R
/**
 * 重载 2: Unwrap 未设置或设置为 true
 */
export declare function extendRef<
  R extends Ref<any>,
  Extend extends object,
  Options extends ExtendRefOptions,
>(ref: R, extend: Extend, options?: Options): Extend & R
```
