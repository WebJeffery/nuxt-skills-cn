---
category: Reactivity
alias: createReactiveFn
---

# reactify

将普通函数转换为响应式函数。转换后的函数接受 refs 作为参数并返回 ComputedRef,具有适当的类型。

::: tip
有兴趣查看一些应用程序或寻找一些预反应函数?

查看 [⚗️ Vue Chemistry](https://github.com/antfu/vue-chemistry)!
:::

## 用法

基本示例

```ts
import { reactify } from '@vueuse/core'
import { shallowRef } from 'vue'

// 普通函数
function add(a: number, b: number): number {
  return a + b
}

// 现在它接受 refs 并返回一个 computed ref
// (a: number | Ref<number>, b: number | Ref<number>) => ComputedRef<number>
const reactiveAdd = reactify(add)

const a = shallowRef(1)
const b = shallowRef(2)
const sum = reactiveAdd(a, b)

console.log(sum.value) // 3

a.value = 5

console.log(sum.value) // 7
```

实现响应式[勾股定理](https://en.wikipedia.org/wiki/Pythagorean_theorem)的示例。

<!-- eslint-skip -->

```ts
import { reactify } from '@vueuse/core'
import { shallowRef } from 'vue'

const pow = reactify(Math.pow)
const sqrt = reactify(Math.sqrt)
const add = reactify((a: number, b: number) => a + b)

const a = shallowRef(3)
const b = shallowRef(4)
const c = sqrt(add(pow(a, 2), pow(b, 2)))
console.log(c.value) // 5

// 5:12:13
a.value = 5
b.value = 12
console.log(c.value) // 13
```

您也可以这样做:

```ts
import { reactify } from '@vueuse/core'
import { shallowRef } from 'vue'

function pythagorean(a: number, b: number) {
  return Math.sqrt(a ** 2 + b ** 2)
}

const a = shallowRef(3)
const b = shallowRef(4)

const c = reactify(pythagorean)(a, b)
console.log(c.value) // 5
```

另一个制作响应式 `stringify` 的示例

```ts
import { reactify } from '@vueuse/core'
import { shallowRef } from 'vue'

const stringify = reactify(JSON.stringify)

const obj = shallowRef(42)
const dumped = stringify(obj)

console.log(dumped.value) // '42'

obj.value = { foo: 'bar' }

console.log(dumped.value) // '{"foo":"bar"}'
```

## 类型声明

```ts
export type Reactified<T, Computed extends boolean> = T extends (
  ...args: infer A
) => infer R
  ? (
      ...args: {
        [K in keyof A]: Computed extends true
          ? MaybeRefOrGetter<A[K]>
          : MaybeRef<A[K]>
      }
    ) => ComputedRef<R>
  : never
export type ReactifyReturn<
  T extends AnyFn = AnyFn,
  K extends boolean = true,
> = Reactified<T, K>
export interface ReactifyOptions<T extends boolean> {
  /**
   * 接受将函数作为响应式 getter
   *
   * @default true
   */
  computedGetter?: T
}
/**
 * 将普通函数转换为响应式函数。
 * 转换后的函数接受 refs 作为参数
 * 并返回 ComputedRef,具有适当的类型。
 *
 * @param fn - 源函数
 * @param options - 选项
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function reactify<T extends AnyFn, K extends boolean = true>(
  fn: T,
  options?: ReactifyOptions<K>,
): ReactifyReturn<T, K>
/** @deprecated 使用 `reactify` 代替 */
export declare const createReactiveFn: typeof reactify
```
