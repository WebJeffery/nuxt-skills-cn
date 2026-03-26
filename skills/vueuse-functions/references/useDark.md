---
category: Browser
related:
  - useColorMode
  - usePreferredDark
  - useStorage
---

# useDark

具有自动数据持久化的响应式暗模式。

<CourseLink href="https://vueschool.io/lessons/theming-with-vueuse-usedark-and-usecolormode?friend=vueuse">通过 Vue School 的这个免费视频课程学习 useDark！</CourseLink>

## 基本用法

```ts
import { useDark, useToggle } from '@vueuse/core'

const isDark = useDark()
const toggleDark = useToggle(isDark)
```

## 行为

`useDark` 结合了 `usePreferredDark` 和 `useStorage`。在启动时，它从 localStorage/sessionStorage（键是可配置的）读取值以查看是否有用户配置的颜色方案，如果没有，它将使用用户的系统偏好设置。当您更改 `isDark` ref 时，它将更新相应元素的属性，然后将首选项存储到存储（默认键：`vueuse-color-scheme`）以进行持久化。

> 请注意 `useDark` 只为您处理 DOM 属性更改，以便在 CSS 中应用适当的选择器。它**不会**为您处理实际的样式、主题或 CSS。

## 配置

默认情况下，它使用 [Tailwind CSS 偏好的暗模式](https://tailwindcss.com/docs/dark-mode#toggling-dark-mode-manually)，当将类 `dark` 应用于 `html` 标签时启用暗模式，例如：

```html
<!--light-->
<html>
  ...
</html>

<!--dark-->
<html class="dark">
  ...
</html>
```

尽管如此，您也可以自定义它以使其适用于大多数 CSS 框架。

例如：

```ts
import { useDark } from '@vueuse/core'
// ---cut---
const isDark = useDark({
  selector: 'body',
  attribute: 'color-scheme',
  valueDark: 'dark',
  valueLight: 'light',
})
```

将像这样工作

```html
<!--light-->
<html>
  <body color-scheme="light">
    ...
  </body>
</html>

<!--dark-->
<html>
  <body color-scheme="dark">
    ...
  </body>
</html>
```

如果上述配置仍然不适合您的需求，您可以使用 `onChanged` 选项来完全控制如何处理更新。

```ts
import { useDark } from '@vueuse/core'
// ---cut---
const isDark = useDark({
  onChanged(dark) {
    // 更新 dom，调用 API 或其他操作
  },
})
```

## 组件用法

```vue
<template>
  <UseDark v-slot="{ isDark, toggleDark }">
    <button @click="toggleDark()">
      Is Dark: {{ isDark }}
    </button>
  </UseDark>
</template>
```

## 类型声明

```ts
export interface UseDarkOptions extends Omit<
  UseColorModeOptions<BasicColorSchema>,
  "modes" | "onChanged"
> {
  /**
   * 当 isDark=true 时应用于目标元素的值
   *
   * @default 'dark'
   */
  valueDark?: string
  /**
   * 当 isDark=false 时应用于目标元素的值
   *
   * @default ''
   */
  valueLight?: string
  /**
   * 处理更新的自定义处理程序。
   * 指定时，默认行为将被覆盖。
   *
   * @default undefined
   */
  onChanged?: (
    isDark: boolean,
    defaultHandler: (mode: BasicColorSchema) => void,
    mode: BasicColorSchema,
  ) => void
}
export type UseDarkReturn = WritableComputedRef<boolean>
/**
 * 具有自动数据持久化的响应式暗模式。
 *
 * @see https://vueuse.org/useDark
 * @param options
 */
export declare function useDark(options?: UseDarkOptions): UseDarkReturn
```
