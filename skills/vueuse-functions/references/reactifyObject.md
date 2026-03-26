---
category: Reactivity
---

# reactifyObject

将 `reactify` 应用于对象

## 用法

```ts
import { reactifyObject } from '@vueuse/core'

const reactifiedConsole = reactifyObject(console)

const a = ref('42')

reactifiedConsole.log(a) // 不再需要 `.value`
```

## 类型声明

```ts
export type ReactifyNested<
  T,
  Keys extends keyof T = keyof T,
  S extends boolean = true,
> = {
  [K in Keys]: T[K] extends AnyFn ? Reactified<T[K], S> : T[K]
}
export type ReactifyObjectReturn<
  T,
  Keys extends keyof T,
  S extends boolean = true,
> = ReactifyNested<T, Keys, S>
export interface ReactifyObjectOptions<
  T extends boolean,
> extends ReactifyOptions<T> {
  /**
   * 包括来自 Object.getOwnPropertyNames 的名称
   *
   * @default true
   */
  includeOwnProperties?: boolean
}
/**
 * 将 `reactify` 应用于对象
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function reactifyObject<T extends object, Keys extends keyof T>(
  obj: T,
  keys?: (keyof T)[],
): ReactifyObjectReturn<T, Keys, true>
export declare function reactifyObject<
  T extends object,
  S extends boolean = true,
>(
  obj: T,
  options?: ReactifyObjectOptions<S>,
): ReactifyObjectReturn<T, keyof T, S>
```
