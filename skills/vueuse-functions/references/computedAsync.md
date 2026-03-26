---
category: Reactivity
alias: asyncComputed
---

# computedAsync

用于异步函数的计算属性。

## 用法

```ts
import { computedAsync } from '@vueuse/core'
import { shallowRef } from 'vue'

const name = shallowRef('jack')

const userInfo = computedAsync(
  async () => {
    return await mockLookUp(name.value)
  },
  null, // 初始状态
)
```

### 评估状态

传递一个 ref 来跟踪异步函数当前是否正在评估。

```ts
import { computedAsync } from '@vueuse/core'
import { shallowRef } from 'vue'

const evaluating = shallowRef(false)

const userInfo = computedAsync(
  async () => { /* 你的逻辑 */ },
  null,
  evaluating, // 也可以通过选项传递: { evaluating }
)
```

### onCancel

当计算源在上一个异步函数解析之前发生变化时,您可能想要取消上一个函数。以下示例展示了如何与 fetch API 结合使用。

```ts
import { computedAsync } from '@vueuse/core'
import { shallowRef } from 'vue'

const packageName = shallowRef('@vueuse/core')

const downloads = computedAsync(async (onCancel) => {
  const abortController = new AbortController()

  onCancel(() => abortController.abort())

  return await fetch(
    `https://api.npmjs.org/downloads/point/last-week/${packageName.value}`,
    { signal: abortController.signal },
  )
    .then(response => response.ok ? response.json() : { downloads: '—' })
    .then(result => result.downloads)
}, 0)
```

### 懒加载

默认情况下,`computedAsync` 会在创建时立即开始解析。指定 `lazy: true` 使其在首次访问时才开始解析。

```ts
import { computedAsync } from '@vueuse/core'
import { shallowRef } from 'vue'

const evaluating = shallowRef(false)

const userInfo = computedAsync(
  async () => { /* 你的逻辑 */ },
  null,
  { lazy: true, evaluating },
)
```

### 错误处理

使用 `onError` 回调来处理异步函数中的错误。

```ts
import { computedAsync } from '@vueuse/core'
import { shallowRef } from 'vue'

const name = shallowRef('jack')

const userInfo = computedAsync(
  async () => {
    return await mockLookUp(name.value)
  },
  null,
  {
    onError(e) {
      console.error('Failed to fetch user info', e)
    },
  },
)
```

### 浅层 Ref

默认情况下,`computedAsync` 在内部使用 `shallowRef`。设置 `shallow: false` 以使用深层 ref。

```ts
import { computedAsync } from '@vueuse/core'
import { shallowRef } from 'vue'

const name = shallowRef('jack')

const userInfo = computedAsync(
  async () => {
    return await fetchNestedData(name.value)
  },
  null,
  { shallow: false }, // 启用深度响应式
)
```

## 注意事项

- 就像 Vue 内置的 `computed` 函数一样,`computedAsync` 会进行依赖跟踪,并在依赖项发生变化时自动重新评估。但是请注意,只有第一次调用堆栈中引用的依赖项才会被考虑。换句话说:**异步访问的依赖项不会触发异步计算值的重新评估。**

- 与 Vue 内置的 `computed` 函数相反,无论其结果当前是否被跟踪,只要依赖项发生变化,就会触发异步计算值的重新评估。

## 类型声明

```ts
/**
 * 处理重叠的异步评估。
 *
 * @param cancelCallback 当在之前的评估完成之前触发计算值的重新评估时调用提供的回调
 */
export type AsyncComputedOnCancel = (cancelCallback: Fn) => void
export interface AsyncComputedOptions<
  Lazy = boolean,
> extends ConfigurableFlushSync {
  /**
   * 是否应该懒加载评估值
   *
   * @default false
   */
  lazy?: Lazy
  /**
   * 传递 ref 以接收异步评估的更新
   */
  evaluating?: Ref<boolean>
  /**
   * 使用 shallowRef
   *
   * @default true
   */
  shallow?: boolean
  /**
   * 捕获错误时的回调。
   */
  onError?: (e: unknown) => void
}
/**
 * 创建异步计算依赖。
 *
 * @see https://vueuse.org/computedAsync
 * @param evaluationCallback     生成计算值的返回 promise 的回调
 * @param initialState           初始状态,在第一次评估完成之前使用
 * @param optionsOrRef           附加选项或传递的 ref 以接收异步评估的更新
 */
export declare function computedAsync<T>(
  evaluationCallback: (onCancel: AsyncComputedOnCancel) => T | Promise<T>,
  initialState: T,
  optionsOrRef: AsyncComputedOptions<true>,
): ComputedRef<T>
export declare function computedAsync<T>(
  evaluationCallback: (onCancel: AsyncComputedOnCancel) => T | Promise<T>,
  initialState: undefined,
  optionsOrRef: AsyncComputedOptions<true>,
): ComputedRef<T | undefined>
export declare function computedAsync<T>(
  evaluationCallback: (onCancel: AsyncComputedOnCancel) => T | Promise<T>,
  initialState: T,
  optionsOrRef?: Ref<boolean> | AsyncComputedOptions,
): Ref<T>
export declare function computedAsync<T>(
  evaluationCallback: (onCancel: AsyncComputedOnCancel) => T | Promise<T>,
  initialState?: undefined,
  optionsOrRef?: Ref<boolean> | AsyncComputedOptions,
): Ref<T | undefined>
/** @deprecated 使用 `computedAsync` 代替 */
export declare const asyncComputed: typeof computedAsync
```
