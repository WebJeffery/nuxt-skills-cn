---
category: '@RxJS'
---

# useSubscription

使用 RxJS [`Subscription`](https://rxjs.dev/guide/subscription)，而无需担心取消订阅或创建内存泄漏。

## 用法

<!-- TODO: 如果启用 twoslash，则导入 rxjs 错误 -->

```ts no-twoslash
import { useSubscription } from '@vueuse/rxjs'
import { interval } from 'rxjs'

const count = ref(0)

// useSubscription 调用在卸载组件之前调用 unsubscribe 方法
useSubscription(
  interval(1000)
    .subscribe(() => {
      count.value++
      console.log(count)
    }),
)
```

## 类型声明

```ts
export declare function useSubscription(subscription: Unsubscribable): void
```
