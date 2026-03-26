---
category: Sensors
---

# useBattery

响应式 [Battery Status API](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API),通常称为 Battery API,提供有关系统电池充电电量的信息,并让您在电池电量或充电状态更改时通过发送的事件收到通知。这可用于调整应用程序的资源使用,以在电池电量低时减少电池消耗,或在电池耗尽之前保存更改以防止数据丢失。

## 用法

```ts
import { useBattery } from '@vueuse/core'

const { isSupported, charging, chargingTime, dischargingTime, level } = useBattery()
```

| 状态           | 类型      | 描述                                                       |
| --------------- | --------- | ----------------------------------------------------------------- |
| isSupported     | `Boolean` | 如果当前浏览器支持 Battery Status API。    |
| charging        | `Boolean` | 设备当前是否正在充电。                              |
| chargingTime    | `Number`  | 设备充满电所需的秒数。     |
| dischargingTime | `Number`  | 设备完全放电前的秒数。 |
| level           | `Number`  | 介于 0 和 1 之间的数字,表示当前充电电量。   |

::: warning 浏览器支持
Battery Status API 的浏览器支持有限。它目前仅在基于 Chromium 的浏览器中可用。在使用值之前始终检查 `isSupported`。
:::

## 用例

我们的应用程序通常不考虑电池电量,我们可以对应用程序进行一些调整,以便对低电量用户更友好。

- 触发特殊的"暗模式"省电主题设置。
- 停止在新闻源中自动播放视频。
- 禁用一些不重要的后台工作线程。
- 限制网络调用并减少 CPU/内存消耗。

## 组件用法

```vue
<template>
  <UseBattery v-slot="{ isSupported, charging, level }">
    <div v-if="isSupported">
      <p>Is Charging: {{ charging }}</p>
      <p>Battery Level: {{ (level * 100).toFixed(0) }}%</p>
    </div>
    <div v-else>
      Battery API not supported
    </div>
  </UseBattery>
</template>
```

## 类型声明

```ts
export interface UseBatteryOptions extends ConfigurableNavigator {}
export interface UseBatteryReturn extends Supportable {
  charging: ShallowRef<boolean>
  chargingTime: ShallowRef<number>
  dischargingTime: ShallowRef<number>
  level: ShallowRef<number>
}
export interface BatteryManager extends EventTarget {
  charging: boolean
  chargingTime: number
  dischargingTime: number
  level: number
}
/**
 * 响应式 Battery Status API。
 *
 * @see https://vueuse.org/useBattery
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useBattery(
  options?: UseBatteryOptions,
): UseBatteryReturn
```
