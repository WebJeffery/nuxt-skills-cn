---
category: '@RxJS'
---

# toObserver

将 `ref` 转换为 RxJS [Observer](https://rxjs.dev/guide/observer) 的糖函数。

## 用法

<!-- TODO: import rxjs error if enable twoslash -->

```ts no-twoslash
import { from, fromEvent, toObserver, useSubscription } from '@vueuse/rxjs'
import { interval } from 'rxjs'
import { map, mapTo, startWith, takeUntil, withLatestFrom } from 'rxjs/operators'
import { shallowRef, useTemplateRef } from 'vue'

const count = shallowRef(0)
const button = useTemplateRef('buttonRef')

useSubscription(
  interval(1000)
    .pipe(
      mapTo(1),
      takeUntil(fromEvent(button, 'click')),
      withLatestFrom(from(count).pipe(startWith(0))),
      map(([curr, total]) => curr + total),
    )
    .subscribe(toObserver(count)), // 与 ).subscribe(val => (count.value = val)) 相同
)
```

## 类型声明

```ts
export declare function toObserver<T>(value: Ref<T>): NextObserver<T>
```
