---
category: Animation
---

# useTransition

值之间的过渡

## 用法

定义一个要跟踪的源值，当更改时，输出将过渡到新值。如果在过渡进行时源更改，新的过渡将从上一个过渡被中断的地方开始。

```ts
import { TransitionPresets, useTransition } from '@vueuse/core'
import { shallowRef } from 'vue'

const source = shallowRef(0)

const output = useTransition(source, {
  duration: 1000,
  easing: TransitionPresets.easeInOutCubic,
})
```

可以使用 [三次贝塞尔曲线](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function/cubic-bezier#description) 自定义过渡缓动。

```ts
import { useTransition } from '@vueuse/core'
// ---cut---
useTransition(source, {
  easing: [0.75, 0, 0.25, 1],
})
```

以下过渡可通过 `TransitionPresets` 常量获得。

- [`linear`](https://cubic-bezier.com/#0,0,1,1)
- [`easeInSine`](https://cubic-bezier.com/#.12,0,.39,0)
- [`easeOutSine`](https://cubic-bezier.com/#.61,1,.88,1)
- [`easeInOutSine`](https://cubic-bezier.com/#.37,0,.63,1)
- [`easeInQuad`](https://cubic-bezier.com/#.11,0,.5,0)
- [`easeOutQuad`](https://cubic-bezier.com/#.5,1,.89,1)
- [`easeInOutQuad`](https://cubic-bezier.com/#.45,0,.55,1)
- [`easeInCubic`](https://cubic-bezier.com/#.32,0,.67,0)
- [`easeOutCubic`](https://cubic-bezier.com/#.33,1,.68,1)
- [`easeInOutCubic`](https://cubic-bezier.com/#.65,0,.35,1)
- [`easeInQuart`](https://cubic-bezier.com/#.5,0,.75,0)
- [`easeOutQuart`](https://cubic-bezier.com/#.25,1,.5,1)
- [`easeInOutQuart`](https://cubic-bezier.com/#.76,0,.24,1)
- [`easeInQuint`](https://cubic-bezier.com/#.64,0,.78,0)
- [`easeOutQuint`](https://cubic-bezier.com/#.22,1,.36,1)
- [`easeInOutQuint`](https://cubic-bezier.com/#.83,0,.17,1)
- [`easeInExpo`](https://cubic-bezier.com/#.7,0,.84,0)
- [`easeOutExpo`](https://cubic-bezier.com/#.16,1,.3,1)
- [`easeInOutExpo`](https://cubic-bezier.com/#.87,0,.13,1)
- [`easeInCirc`](https://cubic-bezier.com/#.55,0,1,.45)
- [`easeOutCirc`](https://cubic-bezier.com/#0,.55,.45,1)
- [`easeInOutCirc`](https://cubic-bezier.com/#.85,0,.15,1)
- [`easeInBack`](https://cubic-bezier.com/#.36,0,.66,-.56)
- [`easeOutBack`](https://cubic-bezier.com/#.34,1.56,.64,1)
- [`easeInOutBack`](https://cubic-bezier.com/#.68,-.6,.32,1.6)

对于更复杂的缓动，可以提供自定义函数。

```ts
import { useTransition } from '@vueuse/core'
// ---cut---
function easeOutElastic(n) {
  return n === 0
    ? 0
    : n === 1
      ? 1
      : (2 ** (-10 * n)) * Math.sin((n * 10 - 0.75) * ((2 * Math.PI) / 3)) + 1
}

useTransition(source, {
  easing: easeOutElastic,
})
```

默认情况下，`source` 必须是数字或数字数组。对于更复杂的值，定义自定义 `interpolation` 函数。例如，以下将过渡 Three.js 旋转。

```ts
import { useTransition } from '@vueuse/core'
// ---cut---
import { Quaternion } from 'three'

const source = ref(new Quaternion())

const output = useTransition(source, {
  interpolation: (q1, q2, t) => new Quaternion().slerpQuaternions(q1, q2, t)
})
```

要控制过渡何时开始，设置 `delay` 值。要在过渡周围编排行为，定义 `onStarted` 或 `onFinished` 回调。

```ts
import { useTransition } from '@vueuse/core'
// ---cut---
useTransition(source, {
  delay: 1000,
  onStarted() {
    // 在过渡开始后调用
  },
  onFinished() {
    // 在过渡结束后调用
  },
})
```

要停止过渡，定义一个布尔 `disabled` 属性。请注意，这与 `duration` 为 `0` 不同。禁用的过渡**同步**跟踪源值。它们不遵守 `delay`，并且不会触发 `onStarted` 或 `onFinished` 回调。

为了获得更多控制，可以通过 `transition` 函数手动执行过渡。此函数返回一个在过渡完成时解析的 promise。可以通过定义返回真值的 `abort` 函数来取消手动过渡。

```ts
import { transition } from '@vueuse/core'

await transition(source, from, to, {
  abort() {
    if (shouldAbort)
      return true
  }
})
```

## 类型声明

```ts
/**
 * 三次贝塞尔点
 */
export type CubicBezierPoints = [number, number, number, number]
/**
 * 缓动函数
 */
export type EasingFunction = (n: number) => number
/**
 * 插值函数
 */
export type InterpolationFunction<T> = (from: T, to: T, t: number) => T
/**
 * 过渡选项
 */
export interface TransitionOptions<T> extends ConfigurableWindow {
  /**
   * 手动中止过渡
   */
  abort?: () => any
  /**
   * 过渡持续时间，以毫秒为单位
   */
  duration?: MaybeRef<number>
  /**
   * 缓动函数或三次贝塞尔点来计算过渡进度
   */
  easing?: MaybeRef<EasingFunction | CubicBezierPoints>
  /**
   * 自定义插值函数
   */
  interpolation?: InterpolationFunction<T>
  /**
   * 缓动函数或三次贝塞尔点来计算过渡进度
   * @deprecated `transition` 选项已弃用，请改用 `easing`。
   */
  transition?: MaybeRef<EasingFunction | CubicBezierPoints>
}
export interface UseTransitionOptions<T> extends TransitionOptions<T> {
  /**
   * 开始过渡前等待的毫秒数
   */
  delay?: MaybeRef<number>
  /**
   * 禁用过渡
   */
  disabled?: MaybeRef<boolean>
  /**
   * 过渡结束后执行的回调
   */
  onFinished?: () => void
  /**
   * 过渡开始后执行的回调
   */
  onStarted?: () => void
}
declare const _TransitionPresets: {
  readonly easeInSine: readonly [0.12, 0, 0.39, 0]
  readonly easeOutSine: readonly [0.61, 1, 0.88, 1]
  readonly easeInOutSine: readonly [0.37, 0, 0.63, 1]
  readonly easeInQuad: readonly [0.11, 0, 0.5, 0]
  readonly easeOutQuad: readonly [0.5, 1, 0.89, 1]
  readonly easeInOutQuad: readonly [0.45, 0, 0.55, 1]
  readonly easeInCubic: readonly [0.32, 0, 0.67, 0]
  readonly easeOutCubic: readonly [0.33, 1, 0.68, 1]
  readonly easeInOutCubic: readonly [0.65, 0, 0.35, 1]
  readonly easeInQuart: readonly [0.5, 0, 0.75, 0]
  readonly easeOutQuart: readonly [0.25, 1, 0.5, 1]
  readonly easeInOutQuart: readonly [0.76, 0, 0.24, 1]
  readonly easeInQuint: readonly [0.64, 0, 0.78, 0]
  readonly easeOutQuint: readonly [0.22, 1, 0.36, 1]
  readonly easeInOutQuint: readonly [0.83, 0, 0.17, 1]
  readonly easeInExpo: readonly [0.7, 0, 0.84, 0]
  readonly easeOutExpo: readonly [0.16, 1, 0.3, 1]
  readonly easeInOutExpo: readonly [0.87, 0, 0.13, 1]
  readonly easeInCirc: readonly [0.55, 0, 1, 0.45]
  readonly easeOutCirc: readonly [0, 0.55, 0.45, 1]
  readonly easeInOutCirc: readonly [0.85, 0, 0.15, 1]
  readonly easeInBack: readonly [0.36, 0, 0.66, -0.56]
  readonly easeOutBack: readonly [0.34, 1.56, 0.64, 1]
  readonly easeInOutBack: readonly [0.68, -0.6, 0.32, 1.6]
}
/**
 * 常见过渡
 *
 * @see https://easings.net
 */
export declare const TransitionPresets: Record<
  keyof typeof _TransitionPresets,
  CubicBezierPoints
> & {
  linear: EasingFunction
}
/**
 * 从一个值过渡到另一个值。
 *
 * @param source
 * @param from
 * @param to
 * @param options
 */
export declare function transition<T>(
  source: Ref<T>,
  from: MaybeRefOrGetter<T>,
  to: MaybeRefOrGetter<T>,
  options?: TransitionOptions<T>,
): PromiseLike<void>
/**
 * 从一个值过渡到另一个值。
 * @deprecated `executeTransition` 函数已弃用，请改用 `transition`。
 *
 * @param source
 * @param from
 * @param to
 * @param options
 */
export declare function executeTransition<T>(
  source: Ref<T>,
  from: MaybeRefOrGetter<T>,
  to: MaybeRefOrGetter<T>,
  options?: TransitionOptions<T>,
): PromiseLike<void>
export declare function useTransition<T extends MaybeRefOrGetter<number>[]>(
  source: [...T],
  options?: UseTransitionOptions<T>,
): ComputedRef<{
  [K in keyof T]: number
}>
export declare function useTransition<T extends MaybeRefOrGetter<number[]>>(
  source: T,
  options?: UseTransitionOptions<T>,
): ComputedRef<number[]>
export declare function useTransition<T>(
  source: MaybeRefOrGetter<T>,
  options?: UseTransitionOptions<T>,
): ComputedRef<T>
```
