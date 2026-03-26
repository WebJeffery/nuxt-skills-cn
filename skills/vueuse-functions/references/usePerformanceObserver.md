---
category: Browser
---

# usePerformanceObserver

观察性能指标。

## 用法

```ts
import { usePerformanceObserver } from '@vueuse/core'

const entrys = ref<PerformanceEntry[]>([])
usePerformanceObserver({
  entryTypes: ['paint'],
}, (list) => {
  entrys.value = list.getEntries()
})
```

## 类型声明

```ts
export type UsePerformanceObserverOptions = PerformanceObserverInit &
  ConfigurableWindow & {
    /**
     * 立即启动观察者。
     *
     * @default true
     */
    immediate?: boolean
  }
/**
 * 观察性能指标。
 *
 * @see https://vueuse.org/usePerformanceObserver
 * @param options
 */
export declare function usePerformanceObserver(
  options: UsePerformanceObserverOptions,
  callback: PerformanceObserverCallback,
): {
  isSupported: UseSupportedReturn
  start: () => void
  stop: () => void
}
```
