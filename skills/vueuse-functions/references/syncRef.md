---
category: Reactivity
related: syncRefs
---

# syncRef

双向 refs 同步。

## 用法

```ts
import { syncRef } from '@vueuse/core'

const a = ref('a')
const b = ref('b')

const stop = syncRef(a, b)

console.log(a.value) // a

b.value = 'foo'

console.log(a.value) // foo

a.value = 'bar'

console.log(b.value) // bar
```

### 单向

```ts
import { syncRef } from '@vueuse/core'

const a = ref('a')
const b = ref('b')

const stop = syncRef(a, b, { direction: 'rtl' })
```

### 自定义转换

```ts
import { syncRef } from '@vueuse/core'

const a = ref(10)
const b = ref(2)

const stop = syncRef(a, b, {
  transform: {
    ltr: left => left * 2,
    rtl: right => right / 2
  }
})

console.log(b.value) // 20

b.value = 30

console.log(a.value) // 15
```

## 类型声明

```ts
type Direction = "ltr" | "rtl" | "both"
type SpecificFieldPartial<T, K extends keyof T> = Partial<Pick<T, K>> &
  Omit<T, K>
/**
 * A = B
 */
type Equal<A, B> = [A] extends [B] ? ([B] extends [A] ? true : false) : false
/**
 * A ∩ B ≠ ∅
 */
type IntersectButNotEqual<A, B> =
  Equal<A, B> extends true ? false : A & B extends never ? false : true
/**
 * A ⊆ B
 */
type IncludeButNotEqual<A, B> =
  Equal<A, B> extends true ? false : A extends B ? true : false
/**
 * A ∩ B = ∅
 */
type NotIntersect<A, B> =
  Equal<A, B> extends true ? false : A & B extends never ? true : false
interface EqualType<
  D extends Direction,
  L,
  R,
  O extends keyof Transform<L, R> = D extends "both" ? "ltr" | "rtl" : D,
> {
  transform?: SpecificFieldPartial<Pick<Transform<L, R>, O>, O>
}
type StrictIncludeMap<
  IncludeType extends "LR" | "RL",
  D extends Exclude<Direction, "both">,
  L,
  R,
> = Equal<[IncludeType, D], ["LR", "ltr"]> &
  Equal<[IncludeType, D], ["RL", "rtl"]> extends true
  ? {
      transform?: SpecificFieldPartial<Pick<Transform<L, R>, D>, D>
    }
  : {
      transform: Pick<Transform<L, R>, D>
    }
type StrictIncludeType<
  IncludeType extends "LR" | "RL",
  D extends Direction,
  L,
  R,
> = D extends "both"
  ? {
      transform: SpecificFieldPartial<
        Transform<L, R>,
        IncludeType extends "LR" ? "ltr" : "rtl"
      >
    }
  : D extends Exclude<Direction, "both">
    ? StrictIncludeMap<IncludeType, D, L, R>
    : never
type IntersectButNotEqualType<D extends Direction, L, R> = D extends "both"
  ? {
      transform: Transform<L, R>
    }
  : D extends Exclude<Direction, "both">
    ? {
        transform: Pick<Transform<L, R>, D>
      }
    : never
type NotIntersectType<D extends Direction, L, R> = IntersectButNotEqualType<
  D,
  L,
  R
>
interface Transform<L, R> {
  ltr: (left: L) => R
  rtl: (right: R) => L
}
type TransformType<D extends Direction, L, R> =
  Equal<L, R> extends true
    ? EqualType<D, L, R>
    : IncludeButNotEqual<L, R> extends true
      ? StrictIncludeType<"LR", D, L, R>
      : IncludeButNotEqual<R, L> extends true
        ? StrictIncludeType<"RL", D, L, R>
        : IntersectButNotEqual<L, R> extends true
          ? IntersectButNotEqualType<D, L, R>
          : NotIntersect<L, R> extends true
            ? NotIntersectType<D, L, R>
            : never
export type SyncRefOptions<
  L,
  R,
  D extends Direction,
> = ConfigurableFlushSync & {
  /**
   * 深度观察
   *
   * @default false
   */
  deep?: boolean
  /**
   * 立即同步值
   *
   * @default true
   */
  immediate?: boolean
  /**
   * 同步的方向。如果您定义了 syncConvertors,值将被重新定义
   *
   * @default 'both'
   */
  direction?: D
} & TransformType<D, L, R>
/**
 * 双向 refs 同步。
 * 从集合论角度限制选项的类型
 * 按以下顺序检查:
 * 1. L = R
 * 2. L ∩ R ≠ ∅
 * 3. L ⊆ R
 * 4. L ∩ R = ∅
 */
export declare function syncRef<L, R, D extends Direction = "both">(
  left: Ref<L>,
  right: Ref<R>,
  ...[options]: Equal<L, R> extends true
    ? [options?: SyncRefOptions<L, R, D>]
    : [options: SyncRefOptions<L, R, D>]
): () => void
```
