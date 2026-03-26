---
category: Animation
---

# useTimeout

在给定时间后变为 `true` 的响应式值。

## 用法

```ts
import { useTimeout } from '@vueuse/core'

const ready = useTimeout(1000)
```

1 秒后，`ready.value` 变为 `true`。

### 带有控制

```ts
import { useTimeout } from '@vueuse/core'

const { ready, start, stop, isPending } = useTimeout(1000, { controls: true })

// 检查超时是否正在进行
console.log(isPending.value) // true

// 停止超时
stop()

// 启动/重启超时
start()
```

### 选项

| 选项      | 类型         | 默认值 | 描述                                      |
| ----------- | ------------ | ------- | ----------------------------------------- |
| `controls`  | `boolean`    | `false` | 暴露 `start`、`stop` 和 `isPending` 控制 |
| `immediate` | `boolean`    | `true`  | 立即启动超时                    |
| `callback`  | `() => void` | —       | 超时完成时调用                |

### 超时回调

```ts
import { useTimeout } from '@vueuse/core'

useTimeout(1000, {
  callback: () => {
    console.log('Timeout completed!')
  },
})
```

### 响应式间隔

超时持续时间可以是响应式的：

```ts
import { useTimeout } from '@vueuse/core'

const duration = ref(1000)
const ready = useTimeout(duration)

// 更改持续时间（仅影响使用控件时的未来超时）
duration.value = 2000
```

## 类型声明

```ts
export interface UseTimeoutOptions<
  Controls extends boolean,
> extends UseTimeoutFnOptions {
  /**
   * 暴露更多控制
   *
   * @default false
   */
  controls?: Controls
  /**
   * 超时回调
   */
  callback?: Fn
}
export type UseTimeoutReturn =
  | ComputedRef<boolean>
  | ({
      readonly ready: ComputedRef<boolean>
    } & Stoppable)
/**
 * @deprecated 使用 UseTimeoutReturn 代替
 */
export type UseTimoutReturn = UseTimeoutReturn
/**
 * 在给定时间后更新值并带有控制。
 *
 * @see   {@link https://vueuse.org/useTimeout}
 * @param interval
 * @param options
 */
export declare function useTimeout(
  interval?: MaybeRefOrGetter<number>,
  options?: UseTimeoutOptions<false>,
): ComputedRef<boolean>
export declare function useTimeout(
  interval: MaybeRefOrGetter<number>,
  options: UseTimeoutOptions<true>,
): {
  ready: ComputedRef<boolean>
} & Stoppable
```
