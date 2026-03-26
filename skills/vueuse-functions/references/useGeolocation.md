---
category: Sensors
---

# useGeolocation

响应式 [Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)。如果用户愿意，它允许用户向 Web 应用程序提供他们的位置。出于隐私原因，会询问用户权限以报告位置信息。

## 用法

```ts
import { useGeolocation } from '@vueuse/core'

const { coords, locatedAt, error, resume, pause } = useGeolocation()
```

| 状态      | 类型                                                                          | 描述                                                              |
| --------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| coords    | [`Coordinates`](https://developer.mozilla.org/en-US/docs/Web/API/Coordinates) | 检索到的位置信息，如纬度和经度 |
| locatedAt | `Date`                                                                        | 上次地理定位调用的时间                                    |
| error     | `string`                                                                      | 地理定位 API 失败时的错误消息。                          |
| resume    | `function`                                                                    | 恢复更新地理定位的控制函数                          |
| pause     | `function`                                                                    | 暂停更新地理定位的控制函数                           |

## 配置

`useGeolocation` 函数接受 [PositionOptions](https://developer.mozilla.org/en-US/docs/Web/API/PositionOptions) 对象作为可选参数。

## 组件用法

```vue
<template>
  <UseGeolocation v-slot="{ coords: { latitude, longitude } }">
    纬度: {{ latitude }}
    经度: {{ longitude }}
  </UseGeolocation>
</template>
```

## Type Declarations

```ts
export interface UseGeolocationOptions
  extends Partial<PositionOptions>, ConfigurableNavigator {
  immediate?: boolean
}
export interface UseGeolocationReturn extends Supportable {
  coords: Ref<Omit<GeolocationPosition["coords"], "toJSON">>
  locatedAt: ShallowRef<number | null>
  error: ShallowRef<GeolocationPositionError | null>
  resume: () => void
  pause: () => void
}
/**
 * Reactive Geolocation API.
 *
 * @see https://vueuse.org/useGeolocation
 * @param options
 */
export declare function useGeolocation(
  options?: UseGeolocationOptions,
): UseGeolocationReturn
```
