---
category: Browser
related:
  - useDark
  - usePreferredDark
  - useStorage
---

# useColorMode

响应式颜色模式(暗/亮/自定义),具有自动数据持久化。

## 基本用法

```ts
import { useColorMode } from '@vueuse/core'

const mode = useColorMode() // Ref<'dark' | 'light'>
```

默认情况下,它将使用 `usePreferredDark`(即 `auto` 模式)与用户的浏览器偏好匹配。当读取 ref 时,默认情况下它将返回当前颜色模式(`dark`、`light` 或您的自定义模式)。通过启用 `emitAuto` 选项,`auto` 模式可以包含在返回的模式中。当写入 ref 时,它将触发 DOM 更新并将颜色模式持久化到本地存储(或您的自定义存储)。您可以传递 `auto` 以设置回自动模式。

```ts
import { useColorMode } from '@vueuse/core'

const mode = useColorMode()
// ---cut---
mode.value // 'dark' | 'light'

mode.value = 'dark' // 更改为暗模式并持久化

mode.value = 'auto' // 更改为自动模式
```

## 配置

```ts
import { useColorMode } from '@vueuse/core'

const mode = useColorMode({
  attribute: 'theme',
  modes: {
    // 自定义颜色
    dim: 'dim',
    cafe: 'cafe',
  },
}) // Ref<'dark' | 'light' | 'dim' | 'cafe'>
```

## 高级用法

您还可以显式访问系统偏好和存储的用户覆盖模式。

```ts
import { useColorMode } from '@vueuse/core'

const { system, store } = useColorMode()

system.value // 'dark' | 'light'
store.value // 'dark' | 'light' | 'auto'

const myColorMode = computed(() => store.value === 'auto' ? system.value : store.value)
```

## 组件用法

```vue
<template>
  <UseColorMode v-slot="color">
    <button @click="color.mode = color.mode === 'dark' ? 'light' : 'dark'">
      模式 {{ color.mode }}
    </button>
  </UseColorMode>
</template>
```

## 类型声明

```ts
export type BasicColorMode = "light" | "dark"
export type BasicColorSchema = BasicColorMode | "auto"
export interface UseColorModeOptions<
  T extends string = BasicColorMode,
> extends UseStorageOptions<T | BasicColorMode> {
  /**
   * 应用到的目标元素的 CSS 选择器
   *
   * @default 'html'
   */
  selector?: string | MaybeElementRef
  /**
   * 应用到目标元素的 HTML 属性
   *
   * @default 'class'
   */
  attribute?: string
  /**
   * 初始颜色模式
   *
   * @default 'auto'
   */
  initialValue?: MaybeRefOrGetter<T | BasicColorSchema>
  /**
   * 将值添加到属性时使用的前缀
   */
  modes?: Partial<Record<T | BasicColorSchema, string>>
  /**
   * 用于处理更新的自定义处理程序。
   * 当指定时,将覆盖默认行为。
   *
   * @default undefined
   */
  onChanged?: (
    mode: T | BasicColorMode,
    defaultHandler: (mode: T | BasicColorMode) => void,
  ) => void
  /**
   * 自定义存储 ref
   *
   * 当提供时,`useStorage` 将被跳过
   */
  storageRef?: Ref<T | BasicColorSchema>
  /**
   * 用于将数据持久化到 localStorage/sessionStorage 的键。
   *
   * 传递 `null` 以禁用持久化
   *
   * @default 'vueuse-color-scheme'
   */
  storageKey?: string | null
  /**
   * 存储对象,可以是 localStorage 或 sessionStorage
   *
   * @default localStorage
   */
  storage?: StorageLike
  /**
   * 从状态发出 `auto` 模式
   *
   * 当设置为 `true` 时,首选模式不会被转换为 `light` 或 `dark`。
   * 当需要知道选择了 `auto` 模式这一事实时,这很有用。
   *
   * @default undefined
   * @deprecated 当需要知道 `auto` 模式时使用 `store.value`
   * @see https://vueuse.org/core/useColorMode/#advanced-usage
   */
  emitAuto?: boolean
  /**
   * 在切换时禁用过渡
   *
   * @see https://paco.me/writing/disable-theme-transitions
   * @default true
   */
  disableTransition?: boolean
}
export type UseColorModeReturn<T extends string = BasicColorMode> = Ref<
  T | BasicColorSchema
> & {
  store: Ref<T | BasicColorSchema>
  system: ComputedRef<BasicColorMode>
  state: ComputedRef<T | BasicColorMode>
}
/**
 * 具有自动数据持久化的响应式颜色模式。
 *
 * @see https://vueuse.org/useColorMode
 * @param options
 */
export declare function useColorMode<T extends string = BasicColorMode>(
  options?: UseColorModeOptions<T>,
): UseColorModeReturn<T>
```
