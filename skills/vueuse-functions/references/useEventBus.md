---
category: Utilities
---

# useEventBus

一个基本的事件总线。

## 用法

```ts
import { useEventBus } from '@vueuse/core'

const bus = useEventBus<string>('news')

function listener(event: string) {
  console.log(`news: ${event}`)
}

// 监听事件
const unsubscribe = bus.on(listener)

// 触发事件
bus.emit('东京奥运会已经开始')

// 取消注册监听器
unsubscribe()
// 或者
bus.off(listener)

// 清除所有监听器
bus.reset()
```

在组件 `setup` 中注册的监听器将在组件卸载时自动取消注册。

## TypeScript

使用 `EventBusKey` 是将事件类型绑定到键的关键，类似于 Vue 的 [`InjectionKey`](https://antfu.me/posts/typed-provide-and-inject-in-vue) 工具。

```ts
// fooKey.ts
import type { EventBusKey } from '@vueuse/core'

export const fooKey: EventBusKey<{ name: foo }> = Symbol('symbol-key')
```

```ts
import { useEventBus } from '@vueuse/core'

import { fooKey } from './fooKey'

const bus = useEventBus(fooKey)

bus.on((e) => {
  // `e` 将是 `{ name: foo }`
})
```

## 类型声明

```ts
export type EventBusListener<T = unknown, P = any> = (
  event: T,
  payload?: P,
) => void
export type EventBusEvents<T, P = any> = Set<EventBusListener<T, P>>
export interface EventBusKey<T> extends Symbol {}
export type EventBusIdentifier<T = unknown> = EventBusKey<T> | string | number
export interface UseEventBusReturn<T, P> {
  /**
   * 订阅事件。调用 emit 时，监听器将执行。
   * @param listener 监听器。
   * @returns 停止函数以移除当前回调。
   */
  on: (listener: EventBusListener<T, P>) => Fn
  /**
   * 类似于 `on`，但只触发一次
   * @param listener 监听器。
   * @returns 停止函数以移除当前回调。
   */
  once: (listener: EventBusListener<T, P>) => Fn
  /**
   * 触发事件，相应的事件监听器将执行。
   * @param event 发送的数据。
   */
  emit: (event?: T, payload?: P) => void
  /**
   * 移除相应的监听器。
   * @param listener 监听器。
   */
  off: (listener: EventBusListener<T>) => void
  /**
   * 清除所有事件
   */
  reset: () => void
}
export declare function useEventBus<T = unknown, P = any>(
  key: EventBusIdentifier<T>,
): UseEventBusReturn<T, P>
```

<!--
Source references:
- https://vueuse.org/core/useEventBus/
-->