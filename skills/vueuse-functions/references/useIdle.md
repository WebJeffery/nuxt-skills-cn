---
category: Sensors
---

# useIdle

跟踪用户是否处于非活动状态。

## 用法

```ts
import { useIdle } from '@vueuse/core'

const { idle, lastActive } = useIdle(5 * 60 * 1000) // 5 分钟

console.log(idle.value) // true 或 false
```

以编程方式重置：

```ts
import { useCounter, useIdle } from '@vueuse/core'
import { watch } from 'vue'

const { inc, count } = useCounter()

const { idle, lastActive, reset } = useIdle(5 * 60 * 1000) // 5 分钟

watch(idle, (idleValue) => {
  if (idleValue) {
    inc()
    console.log(`触发了 ${count.value} 次`)
    reset() // 重新启动空闲计时器。不更改 lastActive 值
  }
})
```

## 组件用法

```vue
<template>
  <UseIdle v-slot="{ idle }" :timeout="5 * 60 * 1000">
    是否空闲: {{ idle }}
  </UseIdle>
</template>
```

## Type Declarations

```ts
export interface UseIdleOptions
  extends ConfigurableWindow, ConfigurableEventFilter {
  /**
   * Event names that listen to for detected user activity
   *
   * @default ['mousemove', 'mousedown', 'resize', 'keydown', 'touchstart', 'wheel']
   */
  events?: WindowEventName[]
  /**
   * Listen for document visibility change
   *
   * @default true
   */
  listenForVisibilityChange?: boolean
  /**
   * Initial state of the ref idle
   *
   * @default false
   */
  initialState?: boolean
}
export interface UseIdleReturn extends Stoppable {
  idle: ShallowRef<boolean>
  lastActive: ShallowRef<number>
  reset: () => void
}
/**
 * Tracks whether the user is being inactive.
 *
 * @see https://vueuse.org/useIdle
 * @param timeout default to 1 minute
 * @param options IdleOptions
 */
export declare function useIdle(
  timeout?: number,
  options?: UseIdleOptions,
): UseIdleReturn
```
