---
category: Time
---

# useCountdown

响应式倒计时计时器（以秒为单位）。

## 用法

```ts
import { useCountdown } from '@vueuse/core'

const countdownSeconds = 5
const { remaining, start, stop, pause, resume } = useCountdown(countdownSeconds, {
  onComplete() {

  },
  onTick() {

  }
})
```

您可以使用 `ref` 来更改初始倒计时。
`start()` 和 `resume()` 也接受新的倒计数值用于下一次倒计时。

```ts
import { useCountdown } from '@vueuse/core'
import { shallowRef } from 'vue'

const countdown = shallowRef(5)
const { start, reset } = useCountdown(countdown, {
})

// 更改倒计数值
countdown.value = 10

// 开始一个新的 2 秒倒计时
start(2)

// 将倒计时重置为 4，但不启动它
reset(4)

// 使用 `countdown` 的当前值开始倒计时
start()
```

## 类型声明

```ts
export interface UseCountdownOptions extends ConfigurableScheduler {
  /**
   *  倒计时间隔（毫秒）。默认为 1000ms。
   *
   * @deprecated 请改用 `scheduler` 选项
   */
  interval?: MaybeRefOrGetter<number>
  /**
   * 倒计时达到 0 时调用的回调函数。
   */
  onComplete?: () => void
  /**
   * 倒计时每次滴答时调用的回调函数。
   */
  onTick?: () => void
  /**
   * 立即开始倒计时
   *
   * @deprecated 请改用 `scheduler` 选项
   * @default false
   */
  immediate?: boolean
}
export interface UseCountdownReturn extends Pausable {
  /**
   * 当前倒计数值。
   */
  remaining: ShallowRef<number>
  /**
   * 将倒计时和 repeatsLeft 重置为其初始值。
   */
  reset: (countdown?: MaybeRefOrGetter<number>) => void
  /**
   * 停止倒计时并重置其状态。
   */
  stop: () => void
  /**
   * 重置倒计时并再次启动它。
   */
  start: (countdown?: MaybeRefOrGetter<number>) => void
}
/**
 * 响应式倒计时计时器（以秒为单位）。
 *
 * @param initialCountdown
 * @param options
 *
 * @see https://vueuse.org/useCountdown
 */
export declare function useCountdown(
  initialCountdown: MaybeRefOrGetter<number>,
  options?: UseCountdownOptions,
): UseCountdownReturn
```
