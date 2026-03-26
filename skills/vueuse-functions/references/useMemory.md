---
category: Browser
---

# useMemory

响应式内存信息。

## 用法

```ts
import { useMemory } from '@vueuse/core'

const { isSupported, memory } = useMemory()
```

## 类型声明

```ts
/**
 * Performance.memory
 *
 * @see https://developer.mozilla.org/en-US/docs/Web/API/Performance/memory
 */
export interface MemoryInfo {
  /**
   * 堆的最大大小（以字节为单位），可用于上下文。
   */
  readonly jsHeapSizeLimit: number
  /**
   *  已分配的堆总大小（以字节为单位）。
   */
  readonly totalJSHeapSize: number
  /**
   * JS 堆的当前活动段（以字节为单位）。
   */
  readonly usedJSHeapSize: number
  [Symbol.toStringTag]: "MemoryInfo"
}
export interface UseMemoryOptions extends ConfigurableScheduler {
  /**
   * 立即启动计时器
   *
   * @deprecated 请改用 `scheduler` 选项
   * @default true
   */
  immediate?: boolean
  /**
   * 调用 `resume` 后立即执行回调
   *
   * @deprecated 请改用 `scheduler` 选项
   * @default false
   */
  immediateCallback?: boolean
  /** @deprecated 请改用 `scheduler` 选项 */
  interval?: number
}
export interface UseMemoryReturn extends Supportable {
  memory: Ref<MemoryInfo | undefined>
}
/**
 * 响应式内存信息。
 *
 * @see https://vueuse.org/useMemory
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useMemory(options?: UseMemoryOptions): UseMemoryReturn
```
