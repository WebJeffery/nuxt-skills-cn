---
category: Animation
---

# useNow

响应式当前 Date 实例。

## 用法

```ts
import { useNow } from '@vueuse/core'

const now = useNow()
```

```ts
import { useNow } from '@vueuse/core'
// ---cut---
const { now, pause, resume } = useNow({ controls: true })
```

## 组件用法

```vue
<template>
  <UseNow v-slot="{ now, pause, resume }">
    Now: {{ now }}
    <button @click="pause()">
      Pause
    </button>
    <button @click="resume()">
      Resume
    </button>
  </UseNow>
</template>
```

## 类型声明

```ts
export interface UseNowOptions<
  Controls extends boolean,
> extends ConfigurableScheduler {
  /**
   * 暴露更多控制
   *
   * @default false
   */
  controls?: Controls
  /**
   * 立即启动时钟
   *
   * @deprecated 请改用 `scheduler` 选项
   * @default true
   */
  immediate?: boolean
  /**
   * 更新间隔（以毫秒为单位），或使用 requestAnimationFrame
   *
   * @deprecated 请改用 `scheduler` 选项
   * @default requestAnimationFrame
   */
  interval?: "requestAnimationFrame" | number
}
export type UseNowReturn<Controls extends boolean> = Controls extends true
  ? {
      now: Ref<Date>
    } & Pausable
  : Ref<Date>
/**
 * 响应式当前 Date 实例。
 *
 * @see https://vueuse.org/useNow
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useNow(options?: UseNowOptions<false>): Ref<Date>
export declare function useNow(options: UseNowOptions<true>): {
  now: Ref<Date>
} & Pausable
```
