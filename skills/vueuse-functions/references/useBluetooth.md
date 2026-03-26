---
category: Browser
---

# useBluetooth

响应式 [Web Bluetooth API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API)。提供连接和与蓝牙低功耗外设交互的能力。

Web Bluetooth API 允许网站使用通用属性配置文件(GATT)通过蓝牙 4 无线标准发现和与设备通信。

注意。它目前在 Android M、Chrome OS、Mac 和 Windows 10 中部分实现。有关浏览器兼容性的完整概述,请参阅 [Web Bluetooth API Browser Compatibility](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API#browser_compatibility)

注意。使用 web bluetooth API 规范时需要注意一些注意事项。请参阅 [Web Bluetooth W3C Draft Report](https://webbluetoothcg.github.io/web-bluetooth/) 以了解有关设备检测和连接的许多注意事项。

注意。此 API 在 Web Workers 中不可用(未通过 WorkerNavigator 暴露)。

## 默认用法

```vue
<script setup lang="ts">
import { useBluetooth } from '@vueuse/core'

const {
  isSupported,
  isConnected,
  device,
  requestDevice,
  server,
  error,
} = useBluetooth({
  acceptAllDevices: true,
})
</script>

<template>
  <button @click="requestDevice()">
    请求蓝牙设备
  </button>
  <div v-if="error">
    错误: {{ error }}
  </div>
</template>
```

### 返回值

| 属性        | 类型                             | 描述                                |
| --------------- | -------------------------------- | ------------------------------------------ |
| `isSupported`   | `ComputedRef<boolean>`           | 是否支持 Web Bluetooth API |
| `isConnected`   | `Ref<boolean>`                   | 设备当前是否已连接    |
| `device`        | `Ref<BluetoothDevice>`           | 已连接的蓝牙设备             |
| `server`        | `Ref<BluetoothRemoteGATTServer>` | 已连接设备的 GATT 服务器   |
| `error`         | `Ref<unknown>`                   | 连接期间发生的任何错误  |
| `requestDevice` | `() => Promise<void>`            | 请求蓝牙设备的函数     |

当设备已配对并连接时,您可以根据需要使用 server 对象。

## 电池电量示例用法

此示例说明了使用 Web Bluetooth API 读取电池电量并从附近的蓝牙设备接收使用蓝牙低功耗广播电池信息的更改通知。

在这里,我们使用 characteristicvaluechanged 事件侦听器来处理读取电池电量特征值。此事件侦听器还将可选地处理即将到来的通知。

```vue
<script setup lang="ts">
import { useBluetooth, useEventListener, watchPausable } from '@vueuse/core'

const {
  isSupported,
  isConnected,
  device,
  requestDevice,
  server,
} = useBluetooth({
  acceptAllDevices: true,
  optionalServices: [
    'battery_service',
  ],
})

const batteryPercent = ref<undefined | number>()

const isGettingBatteryLevels = ref(false)

async function getBatteryLevels() {
  isGettingBatteryLevels.value = true

  // 获取电池服务:
  const batteryService = await server.getPrimaryService('battery_service')

  // 获取当前电池电量
  const batteryLevelCharacteristic = await batteryService.getCharacteristic(
    'battery_level',
  )

  // 监听特征值在 `characteristicvaluechanged` 事件上的更改:
  useEventListener(batteryLevelCharacteristic, 'characteristicvaluechanged', (event) => {
    batteryPercent.value = event.target.value.getUint8(0)
  }, { passive: true })

  // 将接收到的缓冲区转换为数字:
  const batteryLevel = await batteryLevelCharacteristic.readValue()

  batteryPercent.value = await batteryLevel.getUint8(0)
}

const { stop } = watchPausable(isConnected, (newIsConnected) => {
  if (!newIsConnected || !server.value || isGettingBatteryLevels.value)
    return
  // 尝试获取设备的电池电量:
  getBatteryLevels()
  // 我们只想在初始连接时运行此操作,因为我们将使用事件侦听器来处理更新:
  stop()
})
</script>

<template>
  <button @click="requestDevice()">
    请求蓝牙设备
  </button>
</template>
```

更多示例可以在 [Google Chrome's Web Bluetooth Samples](https://googlechrome.github.io/samples/web-bluetooth/) 上找到。

## 类型声明

```ts
export interface UseBluetoothRequestDeviceOptions {
  /**
   *
   * BluetoothScanFilters 数组。此过滤器由 BluetoothServiceUUIDs 数组、name 参数和 namePrefix 参数组成。
   *
   */
  filters?: BluetoothLEScanFilter[] | undefined
  /**
   *
   * BluetoothServiceUUIDs 数组。
   *
   * @see https://developer.mozilla.org/en-US/docs/Web/API/BluetoothRemoteGATTService/uuid
   *
   */
  optionalServices?: BluetoothServiceUUID[] | undefined
}
export interface UseBluetoothOptions
  extends UseBluetoothRequestDeviceOptions, ConfigurableNavigator {
  /**
   *
   * 一个布尔值,指示请求脚本可以接受所有蓝牙设备。默认为 false。
   *
   * !! 这可能会导致在选择器中显示大量不相关的设备,并且因为没有过滤器而浪费能量。
   *
   *
   * 请谨慎使用。
   *
   * @default false
   *
   */
  acceptAllDevices?: boolean
}
export declare function useBluetooth(
  options?: UseBluetoothOptions,
): UseBluetoothReturn
export interface UseBluetoothReturn extends Supportable {
  isConnected: Readonly<ShallowRef<boolean>>
  device: ShallowRef<BluetoothDevice | undefined>
  requestDevice: () => Promise<void>
  server: ShallowRef<BluetoothRemoteGATTServer | undefined>
  error: ShallowRef<unknown | null>
}
```
