---
category: Reactivity
alias: controlledComputed
---

# computedWithControl

显式定义计算的依赖项。

## 用法

```ts twoslash include main
import { computedWithControl } from '@vueuse/core'

const source = ref('foo')
const counter = ref(0)

const computedRef = computedWithControl(
  () => source.value, // 监视 source,与 `watch` 相同
  () => counter.value, // 计算的 getter,与 `computed` 相同
)
```

这样,`counter` 的变化不会触发 `computedRef` 更新,但 `source` ref 会。

```ts
// @include: main
// ---cut---
console.log(computedRef.value) // 0

counter.value += 1

console.log(computedRef.value) // 0

source.value = 'bar'

console.log(computedRef.value) // 1
```

### 手动触发

您还可以通过以下方式手动触发的计算更新:

```ts
// @include: main
// ---cut---
const computedRef = computedWithControl(
  () => source.value,
  () => counter.value,
)

computedRef.trigger()
```

### 深度监视

与 `computed` 不同,`computedWithControl` 默认是浅层的。
您可以指定与 `watch` 相同的选项来控制行为:

```ts
const source = ref({ name: 'foo' })

const computedRef = computedWithControl(
  source,
  () => counter.value,
  { deep: true },
)
```

## 类型声明

```ts
export interface ComputedWithControlRefExtra {
  /**
   * 强制更新计算值。
   */
  trigger: () => void
}
export interface ComputedRefWithControl<T>
  extends ComputedRef<T>, ComputedWithControlRefExtra {}
export interface WritableComputedRefWithControl<T>
  extends WritableComputedRef<T>, ComputedWithControlRefExtra {}
export type ComputedWithControlRef<T = any> =
  | ComputedRefWithControl<T>
  | WritableComputedRefWithControl<T>
export declare function computedWithControl<T>(
  source: WatchSource | MultiWatchSources,
  fn: ComputedGetter<T>,
  options?: WatchOptions,
): ComputedRefWithControl<T>
export declare function computedWithControl<T>(
  source: WatchSource | MultiWatchSources,
  fn: WritableComputedOptions<T>,
  options?: WatchOptions,
): WritableComputedRefWithControl<T>
/** @deprecated 使用 `computedWithControl` 代替 */
export declare const controlledComputed: typeof computedWithControl
```
