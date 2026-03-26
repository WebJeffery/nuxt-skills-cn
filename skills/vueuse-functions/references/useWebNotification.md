---
category: Browser
---

# useWebNotification

响应式 [Notification](https://developer.mozilla.org/en-US/docs/Web/API/notification)

Notifications API 的 Web Notification 接口用于配置和向用户显示桌面通知。

## 用法

::: tip
在应用可以发送通知之前，用户必须授予应用程序执行此操作的权限。用户的操作系统设置也可能阻止预期的通知行为。
:::

```ts
import { useWebNotification } from '@vueuse/core'

const {
  isSupported,
  notification,
  permissionGranted,
  show,
  close,
  onClick,
  onShow,
  onError,
  onClose,
} = useWebNotification({
  title: 'Hello, VueUse world!',
  dir: 'auto',
  lang: 'en',
  renotify: true,
  tag: 'test',
})

if (isSupported.value && permissionGranted.value)
  show()
```

此组合式函数还利用了来自 `@vueuse/shared` 的 createEventHook 工具：

```ts
import { useWebNotification } from '@vueuse/core'

const { onClick, onShow, onError, onClose, } = useWebNotification()
// ---cut---
onClick((evt: Event) => {
  // 对通知 on:click 事件执行某些操作...
})

onShow((evt: Event) => {
  // 对通知 on:show 事件执行某些操作...
})

onError((evt: Event) => {
  // 对通知 on:error 事件执行某些操作...
})

onClose((evt: Event) => {
  // 对通知 on:close 事件执行某些操作...
})
```

## 类型声明

```ts
export interface WebNotificationOptions {
  /**
   * Notification 接口的 title 只读属性指示
   * 通知的标题
   *
   * @default ''
   */
  title?: string
  /**
   * 在构造函数的 options 参数中指定的通知的 body 字符串。
   *
   * @default ''
   */
  body?: string
  /**
   * 在构造函数的 options 参数中指定的通知的文本方向。
   *
   * @default ''
   */
  dir?: "auto" | "ltr" | "rtl"
  /**
   * 在构造函数的 options 参数中指定的通知的语言代码。
   *
   * @default DOMString
   */
  lang?: string
  /**
   * 通知的 ID（如果有），在构造函数的 options 参数中指定。
   *
   * @default ''
   */
  tag?: string
  /**
   * 指定在新通知替换旧通知时是否应通知用户。
   *
   * @default false
   */
  renotify?: boolean
  /**
   * 一个布尔值，指示通知应保持活动状态，直到
   * 用户点击或关闭它，而不是自动关闭。
   *
   * @default false
   */
  requireInteraction?: boolean
  /**
   * Notification 接口的 silent 只读属性指定
   * 通知是否应为静音的，即无论设备设置如何，都不应发出声音或振动。
   *
   * @default false
   */
  silent?: boolean
  /**
   * 为具有振动硬件的设备指定要发射的振动模式。
   * 振动模式，如 Vibration API 规范中所指定
   *
   * @see https://w3c.github.io/vibration/
   */
  vibrate?: number[]
}
export interface UseWebNotificationOptions
  extends ConfigurableWindow, WebNotificationOptions {
  /**
   * 如果未授予权限，则在 onMounted 时请求权限。
   *
   * 可以禁用并在之后调用 `ensurePermissions` 来授予权限。
   *
   * @default true
   */
  requestPermissions?: boolean
}
export interface UseWebNotificationReturn extends Supportable {
  notification: Ref<Notification | null>
  ensurePermissions: () => Promise<boolean | undefined>
  permissionGranted: ShallowRef<boolean>
  show: (
    overrides?: WebNotificationOptions,
  ) => Promise<Notification | undefined>
  close: () => void
  onClick: EventHookOn<Event>
  onShow: EventHookOn<Event>
  onError: EventHookOn<Event>
  onClose: EventHookOn<Event>
}
/**
 * 响应式 useWebNotification
 *
 * @see https://vueuse.org/useWebNotification
 * @see https://developer.mozilla.org/en-US/docs/Web/API/notification
 */
export declare function useWebNotification(
  options?: UseWebNotificationOptions,
): UseWebNotificationReturn
```
