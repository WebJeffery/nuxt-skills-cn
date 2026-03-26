---
category: '@RxJS'
---

# useObservable

使用 RxJS [`Observable`](https://rxjs.dev/guide/observable)，返回 `ref`，并在组件卸载时自动取消订阅。

## 用法

<!-- TODO: 如果启用 twoslash，则导入 rxjs 错误 -->

```ts no-twoslash
import { useObservable } from '@vueuse/rxjs'
import { interval } from 'rxjs'
import { mapTo, scan, startWith } from 'rxjs/operators'

// setup()
const count = useObservable(
  interval(1000).pipe(
    mapTo(1),
    startWith(0),
    scan((total, next) => next + total),
  ),
)
```

### 初始值

您可以提供一个初始值，该值将在 Observable 发出其第一个值之前使用：

```ts no-twoslash
import { useObservable } from '@vueuse/rxjs'
import { interval } from 'rxjs'

const count = useObservable(
  interval(1000),
  { initialValue: 0 },
)
// count.value 为 0，直到第一次发射
```

### 错误处理

如果您想为可能出错的 `Observable` 添加自定义错误处理，可以提供一个可选的 `onError` 配置。如果没有此配置，RxJS 将把提供的 `Observable` 中的任何错误视为"未处理的错误"，并将在新的调用堆栈中抛出并报告给 `window.onerror`（如果您恰好处于 Node 中，则报告给 `process.on('error')`）。

```ts no-twoslash
import { useObservable } from '@vueuse/rxjs'
import { interval } from 'rxjs'
import { map } from 'rxjs/operators'

// setup()
const count = useObservable(
  interval(1000).pipe(
    map((n) => {
      if (n === 10)
        throw new Error('oops')

      return n + n
    }),
  ),
  {
    onError: (err) => {
      console.log(err.message) // "oops"
    },
  },
)
```

### 选项

| 选项         | 类型                 | 描述                              |
| -------------- | -------------------- | ---------------------------------------- |
| `initialValue` | `T`                  | 在 Observable 发出之前使用的值 |
| `onError`      | `(err: any) => void` | Observable 错误的错误处理程序      |

## 类型声明

```ts
export interface UseObservableOptions<I> {
  onError?: (err: any) => void
  /**
   * 如果 observable 尚未发出，则应设置的值。
   */
  initialValue?: I | undefined
}
export declare function useObservable<H, I = undefined>(
  observable: Observable<H>,
  options?: UseObservableOptions<I | undefined>,
): Readonly<Ref<H | I>>
```
