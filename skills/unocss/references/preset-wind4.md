---
name: preset-wind4
description: Tailwind CSS v4 兼容预设，具有增强功能
---

# 预设 Wind4

Tailwind CSS v4 兼容预设。使用现代 CSS 功能增强 preset-wind3。

## 安装

```ts
import { defineConfig, presetWind4 } from 'unocss'

export default defineConfig({
  presets: [
    presetWind4(),
  ],
})
```

## 与 Wind3 的主要区别

### 内置 CSS 重置

不需要 `@unocss/reset` - 重置是内置的：

```ts
// 移除这些导入
import '@unocss/reset/tailwind.css' // ❌ 不需要
import '@unocss/reset/tailwind-compat.css' // ❌ 不需要

// 在配置中启用
presetWind4({
  preflights: {
    reset: true,
  },
})
```

### OKLCH 颜色模型

使用 `oklch` 以获得更好的颜色感知和对比度。与 `presetLegacyCompat` 不兼容。

### 主题 CSS 变量

自动从主题生成 CSS 变量：

```css
:root, :host {
  --spacing: 0.25rem;
  --font-sans: ui-sans-serif, system-ui, sans-serif;
  --colors-black: #000;
  --colors-white: #fff;
  /* ... */
}
```

### @property CSS 规则

使用 `@property` 以获得更好的浏览器优化：

```css
@property --un-text-opacity {
  syntax: '<percentage>';
  inherits: false;
  initial-value: 100%;
}
```

### 主题键更改

| preset-wind3 | preset-wind4 |
|--------------|--------------|
| `fontFamily` | `font` |
| `fontSize` | `text.fontSize` |
| `lineHeight` | `text.lineHeight` 或 `leading` |
| `letterSpacing` | `text.letterSpacing` 或 `tracking` |
| `borderRadius` | `radius` |
| `easing` | `ease` |
| `breakpoints` | `breakpoint` |
| `verticalBreakpoints` | `verticalBreakpoint` |
| `boxShadow` | `shadow` |
| `transitionProperty` | `property` |
| `container.maxWidth` | `containers.maxWidth` |
| 大小属性（`width`、`height` 等） | 统一到 `spacing` |

## 选项

```ts
presetWind4({
  preflights: {
    // 内置重置样式
    reset: true,
    
    // 主题 CSS 变量生成
    theme: 'on-demand', // true | false | 'on-demand'
    
    // @property CSS 规则
    property: true,
  },
})
```

### 主题变量处理

将主题变量的 rem 转换为 px：

```ts
import { createRemToPxProcessor } from '@unocss/preset-wind4/utils'

presetWind4({
  preflights: {
    theme: {
      mode: 'on-demand',
      process: createRemToPxProcessor(),
    }
  },
})

// 也应用于工具类
export default defineConfig({
  postprocess: [createRemToPxProcessor()],
})
```

### 属性层自定义

```ts
presetWind4({
  preflights: {
    property: {
      // 自定义父包装器
      parent: '@layer custom-properties',
      // 自定义选择器
      selector: ':where(*, ::before, ::after)',
    },
  },
})
```

移除 `@supports` 包装器：

```ts
presetWind4({
  preflights: {
    property: {
      parent: false,
    },
  },
})
```

## 生成的图层

| 图层 | 描述 | 顺序 |
|-------|-------------|-------|
| `properties` | CSS `@property` 规则 | -200 |
| `theme` | 主题 CSS 变量 | -150 |
| `base` | 重置/预置样式 | -100 |

## Theme.defaults

重置样式的全局默认配置：

```ts
import type { Theme } from '@unocss/preset-wind4/theme'

const defaults: Theme['default'] = {
  transition: {
    duration: '150ms',
    timingFunction: 'cubic-bezier(0.4, 0, 0.2, 1)',
  },
  font: {
    family: 'var(--font-sans)',
    featureSettings: 'var(--font-sans--font-feature-settings)',
    variationSettings: 'var(--font-sans--font-variation-settings)',
  },
  monoFont: {
    family: 'var(--font-mono)',
    // ...
  },
}
```

## 兼容性说明

### presetRemToPx

不需要 - 使用内置处理器代替：

```ts
presetWind4({
  preflights: {
    theme: {
      process: createRemToPxProcessor(),
    }
  },
})
```

### presetLegacyCompat

由于 `oklch` 颜色模型，与 preset-wind4 **不兼容**。

## 从 Wind3 迁移

1. 根据上表更新主题键
2. 移除 `@unocss/reset` 导入
3. 启用 `preflights.reset: true`
4. 测试颜色输出（oklch vs rgb）
5. 更新任何自定义主题扩展

```ts
// 之前（wind3）
theme: {
  fontFamily: { sans: 'Roboto' },
  fontSize: { lg: '1.125rem' },
  breakpoints: { sm: '640px' },
}

// 之后（wind4）
theme: {
  font: { sans: 'Roboto' },
  text: { lg: { fontSize: '1.125rem' } },
  breakpoint: { sm: '640px' },
}
```

## 何时使用 Wind4

选择 **preset-wind4** 当：
- 开始新项目
- 针对现代浏览器
- 想要内置重置和 CSS 变量
- 遵循 Tailwind v4 约定

选择 **preset-wind3** 当：
- 需要旧版浏览器支持
- 从 Tailwind v3 迁移
- 使用 presetLegacyCompat
- 想要稳定、经过验证的预设

<!--
源参考：
- https://unocss.dev/presets/wind4
-->
