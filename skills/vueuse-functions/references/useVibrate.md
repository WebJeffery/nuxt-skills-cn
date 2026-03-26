---
category: Browser
---

# useVibrate

响应式 [Vibration API](https://developer.mozilla.org/en-US/docs/Web/API/Vibration_API)

大多数现代移动设备都包含振动硬件，这使得软件代码能够通过使设备震动来向用户提供物理反馈。

Vibration API 为 Web 应用程序提供了访问此硬件的能力（如果存在），如果设备不支持它，则不执行任何操作。

## 用法

振动被描述为开关脉冲的模式，这些脉冲的长度可以不同。

该模式可以由描述振动毫秒数的单个整数，或描述振动和暂停模式的整数数组组成。

```ts
import { useVibrate } from '@vueuse/core'

// 这将使设备振动 300 毫秒
// 然后暂停 100 毫秒，然后再使设备振动 300 毫秒：
const { vibrate, stop, isSupported } = useVibrate({ pattern: [300, 100, 300] })

// 开始振动，它将在模式完成时自动停止：
vibrate()

// 但如果您想停止它，可以：
stop()
```

## 类型声明

```ts
export interface UseVibrateOptions
  extends ConfigurableNavigator, ConfigurableScheduler {
  /**
   *
   * 振动模式
   *
   * 值数组描述设备振动和不振动的交替周期。数组中的每个值
   * 都被转换为整数，然后交替解释为
   * 设备应振动的毫秒数和
   * 设备不应振动的毫秒数
   *
   * @default []
   *
   */
  pattern?: MaybeRefOrGetter<Arrayable<number>>
  /**
   * 运行持久振动的间隔，以毫秒为单位
   *
   * 传递 `0` 以禁用
   *
   * @deprecated 请改用 `scheduler` 选项
   * @default 0
   *
   */
  interval: number
}
export interface UseVibrateReturn extends Supportable {
  pattern: MaybeRefOrGetter<Arrayable<number>>
  intervalControls?: Pausable
  vibrate: (pattern?: Arrayable<number>) => void
  stop: () => void
}
/**
 * 响应式振动
 *
 * @see https://vueuse.org/useVibrate
 * @see https://developer.mozilla.org/en-US/docs/Web/API/Vibration_API
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useVibrate(
  options?: UseVibrateOptions,
): UseVibrateReturn
```
