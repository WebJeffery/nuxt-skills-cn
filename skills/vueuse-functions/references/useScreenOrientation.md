---
category: Browser
---

# useScreenOrientation

响应式 [Screen Orientation API](https://developer.mozilla.org/en-US/docs/Web/API/Screen_Orientation_API)。它为 Web 开发人员提供了有关用户当前屏幕方向的信息。

## 用法

```ts
import { useScreenOrientation } from '@vueuse/core'

const {
  isSupported,
  orientation,
  angle,
  lockOrientation,
  unlockOrientation,
} = useScreenOrientation()
```

要锁定方向，您可以将 [OrientationLockType](https://developer.mozilla.org/en-US/docs/Web/API/ScreenOrientation/type) 传递给 lockOrientation 函数：

```ts
import { useScreenOrientation } from '@vueuse/core'

const {
  isSupported,
  orientation,
  angle,
  lockOrientation,
  unlockOrientation,
} = useScreenOrientation()

lockOrientation('portrait-primary')
```

然后再次解锁，使用以下：

```ts
import { useScreenOrientation } from '@vueuse/core'

const { unlockOrientation } = useScreenOrientation()
// ---cut---
unlockOrientation()
```

接受的方向类型是 `"landscape-primary"`、`"landscape-secondary"`、`"portrait-primary"`、`"portrait-secondary"`、`"any"`、`"landscape"`、`"natural"` 和 `"portrait"` 之一。

[Screen Orientation API MDN](https://developer.mozilla.org/en-US/docs/Web/API/Screen_Orientation_API)

## Type Declarations

```ts
export type OrientationType =
  | "portrait-primary"
  | "portrait-secondary"
  | "landscape-primary"
  | "landscape-secondary"
export type OrientationLockType =
  | "any"
  | "natural"
  | "landscape"
  | "portrait"
  | "portrait-primary"
  | "portrait-secondary"
  | "landscape-primary"
  | "landscape-secondary"
export interface ScreenOrientation extends EventTarget {
  lock: (orientation: OrientationLockType) => Promise<void>
  unlock: () => void
  readonly type: OrientationType
  readonly angle: number
  addEventListener: (
    type: "change",
    listener: (this: this, ev: Event) => any,
    useCapture?: boolean,
  ) => void
}
export interface UseScreenOrientationOptions extends ConfigurableWindow {}
export interface UseScreenOrientationReturn extends Supportable {
  orientation: Ref<OrientationType | undefined>
  angle: ShallowRef<number>
  lockOrientation: (type: OrientationLockType) => Promise<void>
  unlockOrientation: () => void
}
/**
 * Reactive screen orientation
 *
 * @see https://vueuse.org/useScreenOrientation
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useScreenOrientation(
  options?: UseScreenOrientationOptions,
): UseScreenOrientationReturn
```
