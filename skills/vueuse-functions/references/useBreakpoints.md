---
category: Browser
---

# useBreakpoints

响应式视口断点。

## 用法

```ts
import { breakpointsTailwind, useBreakpoints } from '@vueuse/core'

const breakpoints = useBreakpoints(breakpointsTailwind)

const smAndLarger = breakpoints.greaterOrEqual('sm') // sm 及更大
const largerThanSm = breakpoints.greater('sm') // 仅大于 sm
const lgAndSmaller = breakpoints.smallerOrEqual('lg') // lg 及更小
const smallerThanLg = breakpoints.smaller('lg') // 仅小于 lg
```

```vue
<script setup lang="ts">
import { useBreakpoints } from '@vueuse/core'

const breakpoints = useBreakpoints({
  mobile: 0, // 可选
  tablet: 640,
  laptop: 1024,
  desktop: 1280,
})

// 可以是 'mobile' 或 'tablet' 或 'laptop' 或 'desktop'
const activeBreakpoint = breakpoints.active()

// true 或 false
const laptop = breakpoints.between('laptop', 'desktop')
</script>

<template>
  <div :class="activeBreakpoint">
    ...
  </div>
</template>
```

### 快捷方法

您可以直接在返回的对象上访问断点作为属性。这些返回响应式 refs。

```ts
const breakpoints = useBreakpoints({
  tablet: 640,
  laptop: 1024,
})

// 等同于使用 min-width 策略的 breakpoints.greaterOrEqual('tablet')
const isTablet = breakpoints.tablet
```

### 策略

`strategy` 选项控制快捷属性的行为:

- `min-width`(默认,移动优先): 当视口 `>= lg` 时,`breakpoints.lg` 为 `true`
- `max-width`(桌面优先): 当视口 `< xl` 时,`breakpoints.lg` 为 `true`

```ts
const breakpoints = useBreakpoints(breakpointsTailwind, {
  strategy: 'max-width', // 桌面优先
})
```

### 可用方法

| 方法                | 描述                                              |
| --------------------- | -------------------------------------------------------- |
| `greaterOrEqual(k)`   | 响应式: 视口宽度 >= 断点                   |
| `greater(k)`          | 响应式: 视口宽度 > 断点                    |
| `smallerOrEqual(k)`   | 响应式: 视口宽度 <= 断点                   |
| `smaller(k)`          | 响应式: 视口宽度 < 断点                    |
| `between(a, b)`       | 响应式: 视口宽度在 a 和 b 之间                 |
| `isGreaterOrEqual(k)` | 非响应式: 立即返回布尔值                |
| `isGreater(k)`        | 非响应式: 立即返回布尔值                |
| `isSmallerOrEqual(k)` | 非响应式: 立即返回布尔值                |
| `isSmaller(k)`        | 非响应式: 立即返回布尔值                |
| `isInBetween(a, b)`   | 非响应式: 立即返回布尔值                |
| `current()`           | 返回所有匹配断点的计算数组       |
| `active()`            | 返回当前活动断点的计算字符串 |

#### 服务器端渲染和 Nuxt

如果您在启用 SSR 的情况下使用 `useBreakpoints`,则需要指定您希望在服务器上和水合之前呈现的屏幕尺寸,以避免水合不匹配

```ts
import { breakpointsTailwind, useBreakpoints } from '@vueuse/core'

const breakpoints = useBreakpoints(breakpointsTailwind, {
  ssrWidth: 768 // 将启用 SSR 模式并像屏幕宽度为 768px 一样呈现
})
```

或者,您可以使用 [`provideSSRWidth`](../useSSRWidth/index.md) 为您的应用程序全局设置此选项

## 预设

- Tailwind: `breakpointsTailwind`
- Bootstrap v5: `breakpointsBootstrapV5`
- Vuetify v2: `breakpointsVuetifyV2`(已弃用: `breakpointsVuetify`)
- Vuetify v3: `breakpointsVuetifyV3`
- Ant Design: `breakpointsAntDesign`
- Quasar v2: `breakpointsQuasar`
- Sematic: `breakpointsSematic`
- Master CSS: `breakpointsMasterCss`
- Prime Flex: `breakpointsPrimeFlex`
- ElementUI / ElementPlus: `breakpointsElement`

_断点预设故意不自动导入,因为它们不以 `use` 开头,以具有 VueUse 的范围。它们必须显式导入:_

```js
import { breakpointsTailwind } from '@vueuse/core'
// 等等
```

## 类型声明

```ts
export * from "./breakpoints"
export type Breakpoints<K extends string = string> = Record<
  K,
  MaybeRefOrGetter<number | string>
>
export interface UseBreakpointsOptions extends ConfigurableWindow {
  /**
   * 用于生成的快捷方法(如 `.lg`)的查询策略
   *
   * 'min-width' - 当视口大于或等于 lg 断点时,.lg 将为 true(移动优先)
   * 'max-width' - 当视口小于 xl 断点时,.lg 将为 true(桌面优先)
   *
   * @default "min-width"
   */
  strategy?: "min-width" | "max-width"
  ssrWidth?: number
}
export type UseBreakpointReturn<K extends string = string> = Record<
  K,
  ComputedRef<boolean>
> & {
  greaterOrEqual: (k: MaybeRefOrGetter<K>) => ComputedRef<boolean>
  smallerOrEqual: (k: MaybeRefOrGetter<K>) => ComputedRef<boolean>
  greater: (k: MaybeRefOrGetter<K>) => ComputedRef<boolean>
  smaller: (k: MaybeRefOrGetter<K>) => ComputedRef<boolean>
  between: (
    a: MaybeRefOrGetter<K>,
    b: MaybeRefOrGetter<K>,
  ) => ComputedRef<boolean>
  isGreater: (k: MaybeRefOrGetter<K>) => boolean
  isGreaterOrEqual: (k: MaybeRefOrGetter<K>) => boolean
  isSmaller: (k: MaybeRefOrGetter<K>) => boolean
  isSmallerOrEqual: (k: MaybeRefOrGetter<K>) => boolean
  isInBetween: (a: MaybeRefOrGetter<K>, b: MaybeRefOrGetter<K>) => boolean
  current: () => ComputedRef<K[]>
  active: () => ComputedRef<K | "">
}
/**
 * 响应式视口断点
 *
 * @see https://vueuse.org/useBreakpoints
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useBreakpoints<K extends string>(
  breakpoints: Breakpoints<K>,
  options?: UseBreakpointsOptions,
): UseBreakpointReturn<K>
```
