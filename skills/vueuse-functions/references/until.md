---
category: Watch
---

# until

承诺的一次性更改观察

## 用法

#### 等待一些异步数据准备就绪

```ts
import { until, useAsyncState } from '@vueuse/core'

const { state, isReady } = useAsyncState(
  fetch('https://jsonplaceholder.typicode.com/todos/1').then(t => t.json()),
  {},
}

;(async () => {
  await until(isReady).toBe(true)

  console.log(state) // state 现在准备好了!
})()
```

#### 等待自定义条件

> 您可以使用 `invoke` 来调用异步函数。

```ts
import { invoke, until, useCounter } from '@vueuse/core'

const { count } = useCounter()

invoke(async () => {
  await until(count).toMatch(v => v > 7)

  alert('Counter 现在大于 7!')
})
```

#### 超时

```ts
import { until } from '@vueuse/core'
// ---cut---
// 将解析直到 ref.value === true 或 1000ms 过去
await until(ref).toBe(true, { timeout: 1000 })

// 如果超时将抛出错误
try {
  await until(ref).toBe(true, { timeout: 1000, throwOnTimeout: true })
  // ref.value === true
}
catch (e) {
  // 超时
}
```

#### 更多示例

```ts
import { until } from '@vueuse/core'
// ---cut---
await until(ref).toBe(true)
await until(ref).toMatch(v => v > 10 && v < 100)
await until(ref).changed()
await until(ref).changedTimes(10)
await until(ref).toBeTruthy()
await until(ref).toBeNull()

await until(ref).not.toBeNull()
await until(ref).not.toBeTruthy()
```

## 类型声明

```ts
export interface UntilToMatchOptions extends ConfigurableFlushSync {
  /**
   * 如果条件不满足,promise 解析/拒绝的毫秒超时时间。
   * 0 表示永不超时
   *
   * @default 0
   */
  timeout?: number
  /**
   * 超时时拒绝 promise
   *
   * @default false
   */
  throwOnTimeout?: boolean
  /**
   * 内部 watch 的 `deep` 选项
   *
   * @default 'false'
   */
  deep?: WatchOptions["deep"]
}
export interface UntilBaseInstance<T, Not extends boolean = false> {
  toMatch: (<U extends T = T>(
    condition: (v: T) => v is U,
    options?: UntilToMatchOptions,
  ) => Not extends true ? Promise<Exclude<T, U>> : Promise<U>) &
    ((
      condition: (v: T) => boolean,
      options?: UntilToMatchOptions,
    ) => Promise<T>)
  changed: (options?: UntilToMatchOptions) => Promise<T>
  changedTimes: (n?: number, options?: UntilToMatchOptions) => Promise<T>
}
type Falsy = false | void | null | undefined | 0 | 0n | ""
export interface UntilValueInstance<
  T,
  Not extends boolean = false,
> extends UntilBaseInstance<T, Not> {
  readonly not: UntilValueInstance<T, Not extends true ? false : true>
  toBe: <P = T>(
    value: MaybeRefOrGetter<P>,
    options?: UntilToMatchOptions,
  ) => Not extends true ? Promise<T> : Promise<P>
  toBeTruthy: (
    options?: UntilToMatchOptions,
  ) => Not extends true ? Promise<T & Falsy> : Promise<Exclude<T, Falsy>>
  toBeNull: (
    options?: UntilToMatchOptions,
  ) => Not extends true ? Promise<Exclude<T, null>> : Promise<null>
  toBeUndefined: (
    options?: UntilToMatchOptions,
  ) => Not extends true ? Promise<Exclude<T, undefined>> : Promise<undefined>
  toBeNaN: (options?: UntilToMatchOptions) => Promise<T>
}
export interface UntilArrayInstance<T> extends UntilBaseInstance<T> {
  readonly not: UntilArrayInstance<T>
  toContains: (
    value: MaybeRefOrGetter<ElementOf<ShallowUnwrapRef<T>>>,
    options?: UntilToMatchOptions,
  ) => Promise<T>
}
/**
 * 承诺的一次性更改观察
 *
 * @see https://vueuse.org/until
 * @example
 * ```
 * const { count } = useCounter()
 *
 * await until(count).toMatch(v => v > 7)
 *
 * alert('Counter 现在大于 7!')
 * ```
 */
export declare function until<T extends unknown[]>(
  r: WatchSource<T> | MaybeRefOrGetter<T>,
): UntilArrayInstance<T>
export declare function until<T>(
  r: WatchSource<T> | MaybeRefOrGetter<T>,
): UntilValueInstance<T>
```
