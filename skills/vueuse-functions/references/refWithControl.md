---
category: Reactivity
alias: controlledRef
related: computedWithControl
---

# refWithControl

对 ref 及其响应性进行细粒度控制。

## 用法

`refWithControl` 使用 `extendRef` 提供两个额外的函数 `get` 和 `set`,以便更好地控制何时应该跟踪/触发响应性。

```ts
import { refWithControl } from '@vueuse/core'

const num = refWithControl(0)
const doubled = computed(() => num.value * 2)

// 就像普通 ref 一样
num.value = 42
console.log(num.value) // 42
console.log(doubled.value) // 84

// 在不触发响应性的情况下设置值
num.set(30, false)
console.log(num.value) // 30
console.log(doubled.value) // 84 (不更新)

// 在不跟踪响应性的情况下获取值
watchEffect(() => {
  console.log(num.peek())
}) // 30

num.value = 50 // watch effect 不会被触发,因为它没有收集任何内容。
console.log(doubled.value) // 100 (再次更新,因为它是响应式设置)
```

### `peek`, `lay`, `untrackedGet`, `silentSet`

我们还提供了一些简写,用于在不跟踪/触发响应性系统的情况下进行 get/set。以下行是等效的。

```ts
import { refWithControl } from '@vueuse/core'
// ---cut---
const foo = refWithControl('foo')
```

```ts
import { refWithControl } from '@vueuse/core'

const foo = refWithControl('foo')
// ---cut---
// 获取
foo.get(false)
foo.untrackedGet()
foo.peek() // `untrackedGet` 的别名
```

```ts
import { refWithControl } from '@vueuse/core'

const foo = refWithControl('foo')
// ---cut---
// 设置
foo.set('bar', false)
foo.silentSet('bar')
foo.lay('bar') // `silentSet` 的别名
```

## 配置

### `onBeforeChange()`

提供 `onBeforeChange` 选项来控制是否应该接受新值。例如:

```ts
import { refWithControl } from '@vueuse/core'
// ---cut---
const num = refWithControl(0, {
  onBeforeChange(value, oldValue) {
    // 不允许在一次操作中更改大于 ±5
    if (Math.abs(value - oldValue) > 5)
      return false // 返回 `false` 以拒绝更改
  },
})

num.value += 1
console.log(num.value) // 1

num.value += 6
console.log(num.value) // 1 (更改已被拒绝)
```

### `onChanged()`

`onChanged` 选项提供了类似于 Vue 的 `watch` 的功能,但与 `watch` 相比开销更小。

```ts
import { refWithControl } from '@vueuse/core'
// ---cut---
const num = refWithControl(0, {
  onChanged(value, oldValue) {
    console.log(value)
  },
})
```

## 类型声明

```ts
export interface ControlledRefOptions<T> {
  /**
   * ref 更改之前的回调函数。
   *
   * 返回 `false` 以拒绝更改。
   */
  onBeforeChange?: (value: T, oldValue: T) => void | boolean
  /**
   * ref 更改之后的回调函数
   *
   * 这同步发生,与 `watch` 相比开销更小
   */
  onChanged?: (value: T, oldValue: T) => void
}
/**
 * 对 ref 及其响应性进行细粒度控制。
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function refWithControl<T>(
  initial: T,
  options?: ControlledRefOptions<T>,
): ShallowUnwrapRef<{
  get: (tracking?: boolean) => T
  set: (value: T, triggering?: boolean) => void
  untrackedGet: () => T
  silentSet: (v: T) => void
  peek: () => T
  lay: (v: T) => void
}> &
  Ref<T, T>
/** @deprecated 使用 `refWithControl` 代替 */
export declare const controlledRef: typeof refWithControl
```
