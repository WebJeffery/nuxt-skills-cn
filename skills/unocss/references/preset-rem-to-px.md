---
name: preset-rem-to-px
description: 将 rem 单位转换为 px 用于工具类
---

# 预设 Rem to Px

将生成的工具类中的 `rem` 单位转换为 `px`。

## 安装

```ts
import { defineConfig, presetRemToPx, presetWind3 } from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
    presetRemToPx(),
  ],
})
```

## 功能

将所有 rem 值转换为 px：

```html
<div class="p-4">
```

不使用预设：
```css
.p-4 { padding: 1rem; }
```

使用预设：
```css
.p-4 { padding: 16px; }
```

## 使用场景

- 需要像素完美设计的项目
- rem 不能很好工作的环境
- 与基于像素的设计系统保持一致
- 电子邮件模板（更好的兼容性）

## 选项

```ts
presetRemToPx({
  // 转换的基础字体大小（默认：16）
  baseFontSize: 16,
})
```

自定义基础：

```ts
presetRemToPx({
  baseFontSize: 14, // 1rem = 14px
})
```

## 与预设 Wind4 一起使用

**注意：** `presetRemToPx` 在 `preset-wind4` 中不需要。使用内置处理器代替：

```ts
import { createRemToPxProcessor } from '@unocss/preset-wind4/utils'

export default defineConfig({
  presets: [
    presetWind4({
      preflights: {
        theme: {
          process: createRemToPxProcessor(),
        }
      },
    }),
  ],
  // 也应用于工具类
  postprocess: [createRemToPxProcessor()],
})
```

## 重要说明

- 顺序很重要：放在生成 rem 值的预设之后
- 影响所有带有 rem 单位的工具类
- 主题中的 rem 值也会被转换

<!--
源参考：
- https://unocss.dev/presets/rem-to-px
- https://unocss.dev/presets/wind4
-->
