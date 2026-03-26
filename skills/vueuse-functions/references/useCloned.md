---
category: Utilities
---

# useCloned

ref 的响应式克隆。默认情况下,它使用 `JSON.parse(JSON.stringify())` 进行克隆。

## 用法

```ts
import { useCloned } from '@vueuse/core'

const original = ref({ key: 'value' })

const { cloned } = useCloned(original)

original.value.key = 'some new value'

console.log(cloned.value.key) // 'value'
```

## 手动克隆

```ts
import { useCloned } from '@vueuse/core'

const original = ref({ key: 'value' })

const { cloned, sync } = useCloned(original, { manual: true })

original.value.key = 'manual'

console.log(cloned.value.key) // 'value'

sync()

console.log(cloned.value.key)// 'manual'
```

## 自定义克隆函数

使用 [`klona`](https://www.npmjs.com/package/klona) 作为示例:

```ts
import { useCloned } from '@vueuse/core'
import { klona } from 'klona'

const original = ref({ key: 'value' })

const { cloned, isModified, sync } = useCloned(original, { clone: klona })
```

## 类型声明

```ts
export interface UseClonedOptions<T = any> extends WatchOptions {
  /**
   * 自定义克隆函数。
   *
   * 默认情况下,它使用 `JSON.parse(JSON.stringify(value))` 进行克隆。
   */
  clone?: (source: T) => T
  /**
   * 手动同步 ref
   *
   * @default false
   */
  manual?: boolean
}
export interface UseClonedReturn<T> {
  /**
   * 克隆的 ref
   */
  cloned: Ref<T>
  /**
   * Ref 指示克隆数据是否已修改
   */
  isModified: Ref<boolean>
  /**
   * 手动将克隆数据与源同步
   */
  sync: () => void
}
export type CloneFn<F, T = F> = (x: F) => T
export declare function cloneFnJSON<T>(source: T): T
export declare function useCloned<T>(
  source: MaybeRefOrGetter<T>,
  options?: UseClonedOptions,
): UseClonedReturn<T>
```
