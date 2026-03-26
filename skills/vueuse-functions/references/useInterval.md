---
category: Animation
---

# useInterval

在每个间隔增加的响应式计数器。

## 用法

```ts
import { useInterval } from '@vueuse/core'

// 计数器每 200ms 增加一次
const counter = useInterval(200)
```

### 带控制

```ts
import { useInterval } from '@vueuse/core'

const { counter, reset, pause, resume, isActive } = useInterval(200, {
  controls: true,
})

// 将计数器重置为 0
reset()

// 暂停/恢复间隔
pause()
resume()
```

### 选项

| 选项      | 类型                      | 默认值 | 描述                                                |
| ----------- | ------------------------- | ------- | ---------------------------------------------------------- |
| `controls`  | `boolean`                 | `false` | 暴露 `pause`、`resume`、`reset` 和 `isActive` 控制 |
| `immediate` | `boolean`                 | `true`  | 立即启动间隔                             |
| `callback`  | `(count: number) => void` | —       | 在每个间隔使用当前计数调用            |

### 响应式间隔

间隔可以是响应式的：

```ts
import { useInterval } from '@vueuse/core'

const intervalMs = ref(1000)
const counter = useInterval(intervalMs)

// 动态更改间隔
intervalMs.value = 500
```

### 每个间隔的回调

```ts
import { useInterval } from '@vueuse/core'

useInterval(1000, {
  callback: (count) => {
    console.log(`滴答 ${count}`)
  },
})
```

## Type Declarations

```ts
export interface UseIntervalOptions<Controls extends boolean> {
  /**
   * Expose more controls
   *
   * @default false
   */
  controls?: Controls
  /**
   * Execute the update immediately on calling
   *
   * @default true
   */
  immediate?: boolean
  /**
   * Callback on every interval
   */
  callback?: (count: number) => void
}
export interface UseIntervalControls {
  counter: ShallowRef<number>
  reset: () => void
}
export type UseIntervalReturn =
  | Readonly<ShallowRef<number>>
  | Readonly<UseIntervalControls & Pausable>
/**
 * Reactive counter increases on every interval
 *
 * @see https://vueuse.org/useInterval
 * @param interval
 * @param options
 */
export declare function useInterval(
  interval?: MaybeRefOrGetter<number>,
  options?: UseIntervalOptions<false>,
): Readonly<ShallowRef<number>>
export declare function useInterval(
  interval: MaybeRefOrGetter<number>,
  options: UseIntervalOptions<true>,
): Readonly<UseIntervalControls & Pausable>
```
