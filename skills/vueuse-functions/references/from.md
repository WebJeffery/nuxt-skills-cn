---
category: '@RxJS'
---

# from / fromEvent

围绕 RxJS 的 [`from()`](https://rxjs.dev/api/index/function/from) 和 [`fromEvent()`](https://rxjs.dev/api/index/function/fromEvent) 的包装器,允许它们接受 `ref`s。

## 用法

<!-- TODO: import rxjs error if enable twoslash -->

```ts no-twoslash
import { from, fromEvent, toObserver, useSubscription } from '@vueuse/rxjs'
import { interval } from 'rxjs'
import { map, mapTo, takeUntil, withLatestFrom } from 'rxjs/operators'
import { shallowRef, useTemplateRef } from 'vue'

const count = shallowRef(0)
const button = useTemplateRef('buttonRef')

useSubscription(
  interval(1000)
    .pipe(
      mapTo(1),
      takeUntil(fromEvent(button, 'click')),
      withLatestFrom(from(count, {
        immediate: true,
        deep: false,
      })),
      map(([curr, total]) => curr + total),
    )
    .subscribe(toObserver(count)), // same as ).subscribe(val => (count.value = val))
)
```

## from

`from` 函数可以接受标准的 RxJS `ObservableInput` 或 Vue `ref`。当传递 ref 时,它会创建一个 Observable,每当 ref 的值发生变化时就会发出。

### Watch 选项

当使用 `from` 与 ref 时,您可以传递 Vue 的 `WatchOptions`:

| Option      | Type                        | Description                        |
| ----------- | --------------------------- | ---------------------------------- |
| `immediate` | `boolean`                   | 立即发出当前值 |
| `deep`      | `boolean`                   | 深度观察嵌套对象        |
| `flush`     | `'pre' \| 'post' \| 'sync'` | 回调刷新的时机       |

## fromEvent

`fromEvent` 函数扩展了 RxJS 的 `fromEvent`,以接受对元素的 ref。当 ref 的值发生变化时(例如,组件挂载后),它会自动订阅新元素。

```ts no-twoslash
import { fromEvent, useSubscription } from '@vueuse/rxjs'
import { useTemplateRef } from 'vue'

const button = useTemplateRef('buttonRef')

// 当 button 元素可用时将自动订阅
useSubscription(
  fromEvent(button, 'click').subscribe(() => {
    console.log('clicked!')
  })
)
```

## 类型声明

```ts
export declare function from<T>(
  value: ObservableInput<T> | Ref<T>,
  watchOptions?: WatchOptions,
): Observable<T>
export declare function fromEvent<T extends HTMLElement | null>(
  value: MaybeRef<T>,
  event: string,
): Observable<Event>
```
