---
category: '@RxJS'
---

# watchExtractedObservable

监视从组合式函数中提取的 RxJS [`Observable`](https://rxjs.dev/guide/observable) 的值。

在 observable 更改时自动取消订阅，并在组件卸载时自动取消订阅。

支持 [`watch`](https://vuejs.org/guide/essentials/watchers.html#basic-example) 的所有重载。

## 用法

<!-- TODO: import rxjs error if enable twoslash -->

```ts no-twoslash
import { watchExtractedObservable } from '@vueuse/rxjs'
import { computed, reactive, useTemplateRef } from 'vue'
import { AudioPlayer } from '../my/libs/AudioPlayer'

// setup()

const audio = useTemplateRef('audio')
const player = computed(() => (audio.value ? new AudioPlayer(audio.value) : null))
const state = reactive({
  progress: 0,
})

watchExtractedObservable(player, p => p.progress$, (percentage) => {
  state.progress = percentage * 100
})
```

如果你想为可能出错的 `Observable` 添加自定义错误处理，可以提供可选的 `onError` 配置。没有这个配置，RxJS 会将提供的 `Observable` 中的任何错误视为"未处理的错误"，并在新的调用堆栈中抛出并报告给 `window.onerror`（如果你恰好在使用 Node，则报告给 `process.on('error')`）。

如果你需要在被监视的 observable 完成时附加特殊行为，也可以提供可选的 `onComplete` 配置。

```ts no-twoslash
import { watchExtractedObservable } from '@vueuse/rxjs'
import { computed, reactive, useTemplateRef } from 'vue'
import { AudioPlayer } from '../my/libs/AudioPlayer'

// setup()

const audio = useTemplateRef('audio')
const player = computed(() => (audio.value ? new AudioPlayer(audio.value) : null))
const state = reactive({
  progress: 0,
})

watchExtractedObservable(player, p => p.progress$, (percentage) => {
  state.progress = percentage * 100
}, {
  onError: (err: unknown) => {
    console.error(err)
  },
  onComplete: () => {
    state.progress = 100 // 或 0，或其他任何值
  },
})
```

如果你愿意，也可以将 `watch` 选项作为最后一个参数传递：

```ts no-twoslash
import { watchExtractedObservable } from '@vueuse/rxjs'
import { computed, reactive, useTemplateRef } from 'vue'
import { AudioPlayer } from '../my/libs/AudioPlayer'

// setup()

const audio = useTemplateRef('audio')
const player = computed(() => (audio.value ? new AudioPlayer(audio.value) : null))
const state = reactive({
  progress: 0,
})

watchExtractedObservable(player, p => p.progress$, (percentage) => {
  state.progress = percentage * 100
}, {
  onError: (err: unknown) => {
    console.error(err)
  }
}, {
  immediate: true
})
```

## 订阅选项

| 选项       | 类型                     | 描述                          |
| ------------ | ------------------------ | ------------------------------------ |
| `onError`    | `(err: unknown) => void` | Observable 错误的错误处理程序  |
| `onComplete` | `() => void`             | 当 Observable 完成时调用 |

## 返回值

返回一个可用于停止监视的 `WatchHandle`：

```ts no-twoslash
import { watchExtractedObservable } from '@vueuse/rxjs'
import { ref } from 'vue'

const source = ref({ data$: null })

const stop = watchExtractedObservable(source, s => s.data$, (data) => {
  console.log(data)
})

// 稍后，停止监视
stop()
```

## 类型声明

```ts
export type OnCleanup = (cleanupFn: () => void) => void
export type WatchExtractedObservableCallback<
  Value,
  OldValue,
  ObservableElement,
> = (
  value: NonNullable<Value>,
  oldValue: OldValue,
  onCleanup: OnCleanup,
) => Observable<ObservableElement>
export interface WatchExtractedObservableOptions {
  onError?: (err: unknown) => void
  onComplete?: () => void
}
export declare function watchExtractedObservable<
  T extends MultiWatchSources,
  E,
  Immediate extends Readonly<boolean> = false,
>(
  sources: [...T],
  extractor: WatchExtractedObservableCallback<
    MapSources<T>,
    MapOldSources<T, Immediate>,
    E
  >,
  callback: (snapshot: E) => void,
  subscriptionOptions?: WatchExtractedObservableOptions,
  watchOptions?: WatchOptions<Immediate>,
): WatchHandle
export declare function watchExtractedObservable<
  T extends Readonly<MultiWatchSources>,
  E,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  extractor: WatchExtractedObservableCallback<
    MapSources<T>,
    MapOldSources<T, Immediate>,
    E
  >,
  callback: (snapshot: E) => void,
  subscriptionOptions?: WatchExtractedObservableOptions,
  watchOptions?: WatchOptions<Immediate>,
): WatchHandle
export declare function watchExtractedObservable<
  T,
  E,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T>,
  extractor: WatchExtractedObservableCallback<
    T,
    Immediate extends true ? T | undefined : T,
    E
  >,
  callback: (snapshot: E) => void,
  subscriptionOptions?: WatchExtractedObservableOptions,
  watchOptions?: WatchOptions<Immediate>,
): WatchHandle
export declare function watchExtractedObservable<
  T extends object,
  E,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  extractor: WatchExtractedObservableCallback<
    T,
    Immediate extends true ? T | undefined : T,
    E
  >,
  callback: (snapshot: E) => void,
  subscriptionOptions?: WatchExtractedObservableOptions,
  watchOptions?: WatchOptions<Immediate>,
): WatchHandle
```
